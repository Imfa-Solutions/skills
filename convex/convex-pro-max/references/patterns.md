# Common Patterns & Anti-Patterns

## Table of Contents

- [Code Organization](#code-organization)
- [Common Patterns](#common-patterns)
- [Anti-Patterns](#anti-patterns)
- [Testing](#testing)
- [Migration](#migration)
- [Client Integration](#client-integration)

---

## Code Organization

### Model pattern — separate business logic from API surface

```
convex/
├── model/
│   ├── users.ts        # Pure TS functions with business logic
│   └── teams.ts
├── users.ts            # Thin public API wrappers
├── teams.ts
├── auth.ts             # getCurrentUser, requireTeamMember helpers
├── schema.ts
├── http.ts
├── crons.ts
└── convex.config.ts
```

```typescript
// convex/model/users.ts — reusable, testable
import { QueryCtx, MutationCtx } from "../_generated/server";
import { Id } from "../_generated/dataModel";

export async function getUser(ctx: QueryCtx | MutationCtx, userId: Id<"users">) {
  return await ctx.db.get(userId);
}

export async function createUser(ctx: MutationCtx, data: { name: string; email: string }) {
  return await ctx.db.insert("users", { ...data, createdAt: Date.now() });
}
```

```typescript
// convex/users.ts — thin wrapper handling validation and auth
import * as Users from "./model/users";
import { getCurrentUser } from "./auth";

export const updateProfile = mutation({
  args: { name: v.optional(v.string()), bio: v.optional(v.string()) },
  returns: v.null(),
  handler: async (ctx, args) => {
    const user = await getCurrentUser(ctx);
    await Users.updateUserProfile(ctx, user._id, args);
    return null;
  },
});
```

### Minimize `ctx.runAction` — use plain functions instead

```typescript
// BAD: unnecessary runAction overhead
await ctx.runAction(internal.orders.validateOrder, args);
await ctx.runAction(internal.orders.chargeCustomer, args);

// GOOD: plain TypeScript functions
import * as Orders from "./model/orders";
await Orders.validateOrder(ctx, order);
await Orders.chargeCustomer(ctx, order);
```

Only use `ctx.runAction` for: Convex Components, or crossing runtimes (default ↔ Node.js).

---

## Common Patterns

### Get-or-create

```typescript
export const getOrCreateUser = mutation({
  args: { email: v.string(), name: v.string() },
  returns: v.id("users"),
  handler: async (ctx, args) => {
    const existing = await ctx.db.query("users")
      .withIndex("by_email", (q) => q.eq("email", args.email)).first();
    if (existing) return existing._id;
    return await ctx.db.insert("users", { ...args, createdAt: Date.now() });
  },
});
```

### Soft delete

```typescript
// Schema
posts: defineTable({
  title: v.string(),
  deletedAt: v.union(v.number(), v.null()),
}).index("active_posts", ["deletedAt"]),

// Delete
await ctx.db.patch(postId, { deletedAt: Date.now() });

// Query active only
await ctx.db.query("posts")
  .withIndex("active_posts", (q) => q.eq("deletedAt", null)).collect();
```

### Audit log

```typescript
async function recordAudit(ctx: MutationCtx, action: string, details: any) {
  const user = await getCurrentUser(ctx);
  await ctx.db.insert("auditLog", {
    userId: user._id, action, details, timestamp: Date.now(),
  });
}

// Usage inside mutations
await recordAudit(ctx, "team.updated", { teamId: id, oldName, newName });
```

### Action → Mutation pattern (external API + DB update)

```typescript
export const processPayment = action({
  args: { userId: v.id("users"), amount: v.number() },
  returns: v.null(),
  handler: async (ctx, args) => {
    // 1. Call external API
    const response = await fetch("https://api.stripe.com/...", { method: "POST", /* ... */ });
    if (!response.ok) throw new ConvexError("Payment failed");
    const payment = await response.json();

    // 2. Update database via internal mutation
    await ctx.runMutation(internal.payments.recordPayment, {
      userId: args.userId, amount: args.amount, stripePaymentId: payment.id,
    });
    return null;
  },
});
```

### Batch operations

```typescript
export const batchCreate = mutation({
  args: { items: v.array(v.object({ name: v.string() })) },
  returns: v.array(v.id("items")),
  handler: async (ctx, args) => {
    return await Promise.all(
      args.items.map((item) => ctx.db.insert("items", { ...item, createdAt: Date.now() }))
    );
  },
});
```

### Streaming large exports (action with paginated reads)

```typescript
export const exportAll = action({
  handler: async (ctx) => {
    let cursor: string | null = null;
    const all: any[] = [];
    do {
      const page = await ctx.runQuery(internal.users.listPaginated, {
        paginationOpts: { numItems: 100, cursor },
      });
      all.push(...page.page);
      cursor = page.isDone ? null : page.continueCursor;
    } while (cursor);
    return all;
  },
});
```

### Reusable auth helpers

```typescript
// convex/auth.ts
export async function getCurrentUser(ctx: QueryCtx | MutationCtx) {
  const identity = await ctx.auth.getUserIdentity();
  if (!identity) throw new ConvexError({ code: "UNAUTHORIZED", message: "Must be logged in" });
  const user = await ctx.db.query("users")
    .withIndex("by_token", (q) => q.eq("tokenIdentifier", identity.tokenIdentifier)).unique();
  if (!user) throw new ConvexError({ code: "USER_NOT_FOUND", message: "User not found" });
  return user;
}

export async function requireTeamMember(ctx: QueryCtx | MutationCtx, teamId: Id<"teams">) {
  const user = await getCurrentUser(ctx);
  const membership = await ctx.db.query("teamMembers")
    .withIndex("by_team_and_user", (q) => q.eq("team", teamId).eq("user", user._id)).unique();
  if (!membership) throw new ConvexError({ code: "FORBIDDEN", message: "Not a team member" });
  return { user, membership };
}
```

### Granular mutations (security)

```typescript
// BAD: one mutation does everything — hard to secure
export const updateTeam = mutation({
  args: { id: v.id("teams"), update: v.object({
    name: v.optional(v.string()), owner: v.optional(v.id("users")),
  }) },
  handler: async (ctx, { id, update }) => { /* ... */ },
});

// GOOD: separate mutations with different auth requirements
export const setTeamName = mutation({ /* members can call */ });
export const transferOwnership = mutation({ /* only owner can call */ });
export const updateBillingPlan = internalMutation({ /* only system can call */ });
```

---

## Anti-Patterns

### God function

```typescript
// BAD: one function handles everything
export const doEverything = mutation({
  args: { action: v.string(), data: v.any() },
  handler: async (ctx, { action, data }) => {
    if (action === "createUser") { /* ... */ }
    else if (action === "updateTeam") { /* ... */ }
  },
});
// GOOD: specific functions per operation
```

### Client-side joins

```typescript
// BAD: multiple round trips from client
const posts = await convex.query(api.posts.list);
const authors = await Promise.all(posts.map(p => convex.query(api.users.get, { id: p.authorId })));

// GOOD: join server-side in a single query
```

### Storing large blobs in documents

```typescript
// BAD: 10MB base64 string in document
await ctx.db.insert("images", { data: imageData });

// GOOD: use Convex file storage
const storageId = await ctx.storage.store(blob);
```

### Missing await

```typescript
// BAD: floating promise — insert may not complete
ctx.db.insert("posts", { title }); // Missing await!

// GOOD
await ctx.db.insert("posts", { title });
// Enforce: @typescript-eslint/no-floating-promises: "error"
```

---

## Testing

```typescript
import { describe, it, expect } from "vitest";
import { convexTest } from "convex-test";
import { api } from "./_generated/api";

describe("Users", () => {
  it("should create user", async () => {
    const t = convexTest(schema);
    const userId = await t.mutation(api.users.create, { name: "Alice", email: "a@b.com" });
    const user = await t.query(api.users.get, { userId });
    expect(user.name).toBe("Alice");
  });

  it("should prevent unauthorized access", async () => {
    const t = convexTest(schema);
    const t1 = t.withIdentity({ subject: "user1" });
    const teamId = await t1.mutation(api.teams.create, { name: "Team 1" });

    const t2 = t.withIdentity({ subject: "user2" });
    await expect(
      t2.mutation(api.teams.update, { id: teamId, name: "Hacked" })
    ).rejects.toThrow("Unauthorized");
  });
});
```

---

## Migration

### Schema migration (add new field with default)

```typescript
export const migrateUsers = internalMutation({
  handler: async (ctx) => {
    const users = await ctx.db.query("users").collect();
    for (const user of users) {
      if (!user.settings) {
        await ctx.db.patch(user._id, {
          settings: { notifications: true, theme: "light" },
        });
      }
    }
  },
});
// Run: npx convex run migrations:migrateUsers
```

For large tables (10k+), use `@convex-dev/migrations` with batching.

### Feature flags

```typescript
export const newFeature = mutation({
  handler: async (ctx, args) => {
    const user = await getCurrentUser(ctx);
    if (user.email.endsWith("@company.com")) {
      // New implementation
    } else {
      // Old implementation
    }
  },
});
```

---

## Client Integration

### React hooks

```typescript
import { useQuery, useMutation, useAction } from "convex/react";
import { api } from "../convex/_generated/api";

function App() {
  const data = useQuery(api.users.get, { userId }); // reactive, auto-updates
  const createUser = useMutation(api.users.create);
  const sendEmail = useAction(api.emails.send);

  // data is undefined while loading, then the result
}
```

### Optimistic updates

Mutations from React/Rust clients execute **in order, one at a time** — no race conditions. Convex automatically handles optimistic UI via reactive queries.
