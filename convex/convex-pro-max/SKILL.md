---
name: convex-pro-max
description: >
  Complete Convex development mastery — functions (queries, mutations, actions, HTTP actions),
  schema design, index optimization, argument/return validation, authentication, security patterns,
  error handling, file storage, scheduling, crons, aggregates, OCC handling, denormalization,
  TypeScript best practices, and production-ready code organization. The definitive Convex skill.
  Use when building any Convex backend: writing functions, designing schemas, optimizing queries,
  handling auth, adding real-time features, setting up webhooks, scheduling jobs, managing file
  uploads, or reviewing/fixing Convex code. Triggers on: convex, query, mutation, action,
  ctx.db, defineSchema, defineTable, v.id, v.string, v.object, withIndex, ConvexError,
  internalMutation, httpAction, ctx.scheduler, ctx.storage, OCC, convex best practices,
  convex functions, convex schema, convex performance, "how do I do X in Convex".
---

# Convex Pro Max

> The definitive guide for building production-ready Convex applications.

## Critical Rules

1. **Always use new function syntax** with `args`, `returns`, and `handler`.
2. **Always validate** args AND returns on public functions.
3. **Always use indexes** — never `.filter()` on the database. Use `.withIndex()`.
4. **Always `await` promises** — enable `@typescript-eslint/no-floating-promises`.
5. **Use `internal*` functions** for scheduled jobs, crons, and sensitive operations.
6. **Never use `ctx.db` in actions** — use `ctx.runQuery`/`ctx.runMutation`.
7. **Actions are NOT transactional** — consolidate reads into single queries, writes into single mutations.
8. **Return `null` explicitly** if a function returns nothing (`returns: v.null()`).
9. **Use `v.id("table")`** not `v.string()` for document IDs.
10. **Install `@convex-dev/eslint-plugin`** — enforces object syntax, arg validators, explicit table IDs, correct runtime imports.

## Function Types

| Type | DB Access | External APIs | Transactional | Cached/Reactive |
|---|---|---|---|---|
| `query` | Read-only via `ctx.db` | No | Yes | Yes |
| `mutation` | Read/Write via `ctx.db` | No | Yes | No |
| `action` | Via `runQuery`/`runMutation` | Yes | No | No |
| `httpAction` | Via `runQuery`/`runMutation` | Yes | No | No |

### Query

```typescript
import { query } from "./_generated/server";
import { v } from "convex/values";

export const getUser = query({
  args: { userId: v.id("users") },
  returns: v.union(v.object({
    _id: v.id("users"), _creationTime: v.number(),
    name: v.string(), email: v.string(),
  }), v.null()),
  handler: async (ctx, args) => {
    return await ctx.db.get(args.userId);
  },
});
```

### Mutation

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export const createTask = mutation({
  args: { title: v.string(), userId: v.id("users") },
  returns: v.id("tasks"),
  handler: async (ctx, args) => {
    return await ctx.db.insert("tasks", {
      title: args.title, userId: args.userId,
      completed: false, createdAt: Date.now(),
    });
  },
});
```

### Action (external APIs)

```typescript
"use node"; // Required for Node.js APIs

import { action } from "./_generated/server";
import { internal } from "./_generated/api";
import { v } from "convex/values";

export const processPayment = action({
  args: { orderId: v.id("orders"), amount: v.number() },
  returns: v.null(),
  handler: async (ctx, args) => {
    const order = await ctx.runQuery(internal.orders.get, { orderId: args.orderId });
    const result = await fetch("https://api.stripe.com/...", { method: "POST", /* ... */ });
    await ctx.runMutation(internal.orders.updateStatus, {
      orderId: args.orderId, status: result.ok ? "paid" : "failed",
    });
    return null;
  },
});
```

### Internal functions

```typescript
import { internalMutation, internalQuery, internalAction } from "./_generated/server";
import { internal } from "./_generated/api"; // for referencing internal functions
import { api } from "./_generated/api";       // for referencing public functions
```

Only callable by other Convex functions, crons, and the dashboard — never by clients.

## Schema & Indexes

```typescript
// convex/schema.ts
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  users: defineTable({
    name: v.string(),
    email: v.string(),
    role: v.union(v.literal("admin"), v.literal("member")),
    settings: v.object({ theme: v.union(v.literal("light"), v.literal("dark")) }),
  })
    .index("by_email", ["email"])
    .index("by_role", ["role"]),

  messages: defineTable({
    channelId: v.id("channels"),
    authorId: v.id("users"),
    content: v.string(),
  })
    .index("by_channel", ["channelId"])
    .index("by_channel_and_author", ["channelId", "authorId"])
    .searchIndex("search_content", { searchField: "content", filterFields: ["channelId"] }),
});
```

**Index rules:**
- Compound indexes are prefix-searchable: `by_channel_and_author` also serves queries by `channelId` alone.
- Don't create `by_channel` separately if `by_channel_and_author` already exists.
- Name convention: `by_field1_and_field2`.
- Nested fields use dot notation: `.index("by_theme", ["settings.theme"])`.
- System fields `_id` and `_creationTime` are automatically available.

## Database Operations

```typescript
// Read
const doc = await ctx.db.get(id);                              // by ID (null if not found)
const docs = await ctx.db.query("table").collect();             // all (bounded)
const first = await ctx.db.query("table").first();              // first or null
const one = await ctx.db.query("table").withIndex(...).unique(); // exactly one (throws if 0 or >1)
const top10 = await ctx.db.query("table").order("desc").take(10);

// Indexed query
const results = await ctx.db.query("messages")
  .withIndex("by_channel", (q) => q.eq("channelId", channelId))
  .order("desc")
  .take(50);

// Write
const id = await ctx.db.insert("table", { ...fields });
await ctx.db.patch(id, { field: newValue });   // partial update
await ctx.db.replace(id, { ...allFields });    // full replace (keeps _id, _creationTime)
await ctx.db.delete(id);
```

## Validators Quick Reference

```typescript
v.string()          v.number()          v.boolean()         v.null()
v.id("tableName")   v.int64()           v.bytes()           v.any()
v.array(v.string())                     v.record(v.string(), v.number())
v.object({ name: v.string(), age: v.optional(v.number()) })
v.union(v.literal("a"), v.literal("b")) // enum-like
v.optional(v.string())                  // field can be omitted
v.nullable(v.string())                  // shorthand for v.union(v.string(), v.null())
```

**Reusable validators:**
```typescript
const roleValidator = v.union(v.literal("admin"), v.literal("member"));
const profileValidator = v.object({ name: v.string(), bio: v.optional(v.string()) });
```

**Extract TypeScript types:**
```typescript
import { Infer } from "convex/values";
type Role = Infer<typeof roleValidator>; // "admin" | "member"
```

**Generated types:**
```typescript
import { Doc, Id } from "./_generated/dataModel";
import { QueryCtx, MutationCtx, ActionCtx } from "./_generated/server";
```

## Authentication & Security

```typescript
// Reusable auth helper
export async function getCurrentUser(ctx: QueryCtx | MutationCtx) {
  const identity = await ctx.auth.getUserIdentity();
  if (!identity) throw new ConvexError({ code: "UNAUTHORIZED", message: "Must be logged in" });
  const user = await ctx.db.query("users")
    .withIndex("by_token", (q) => q.eq("tokenIdentifier", identity.tokenIdentifier))
    .unique();
  if (!user) throw new ConvexError({ code: "USER_NOT_FOUND", message: "User not found" });
  return user;
}
```

**Security rules:**
- Never trust client-provided identifiers — derive user from `ctx.auth`.
- Use granular mutations (separate `setTeamName`, `transferOwnership`) instead of one generic `updateTeam`.
- Use `internalMutation` for crons, scheduled jobs, webhooks — clients cannot call them.
- Convex IDs are unguessable, but still verify authorization.

## Error Handling

```typescript
import { ConvexError } from "convex/values";

// Throw structured errors
throw new ConvexError({ code: "NOT_FOUND", message: "Task not found" });

// Client catches
try { await createUser({ email }); }
catch (error) {
  if (error instanceof ConvexError) { /* error.data.code, error.data.message */ }
}
```

- Dev: full error messages sent to client. Prod: only `ConvexError` data is forwarded; other errors show "Server Error".
- Mutation errors roll back the entire transaction.

## Code Organization

```
convex/
├── schema.ts              # Schema + indexes
├── auth.ts                # getCurrentUser, requireTeamMember helpers
├── users.ts               # Public user API (thin wrappers)
├── teams.ts               # Public team API
├── model/
│   ├── users.ts           # User business logic (pure TS functions)
│   └── teams.ts           # Team business logic
├── http.ts                # HTTP actions (webhooks, APIs)
├── crons.ts               # Cron jobs
└── convex.config.ts       # Component registration
```

**Use plain TypeScript functions** in `model/` instead of `ctx.runAction` for code organization.
Only use `ctx.runAction` when calling Convex components or crossing runtimes.

## Reference Files

- **HTTP actions, scheduling, crons, file storage, runtimes**: See [references/functions-deep.md](references/functions-deep.md)
- **Denormalization, OCC, sharding, N+1, aggregates, query optimization**: See [references/performance.md](references/performance.md)
- **Common patterns, anti-patterns, testing, migration**: See [references/patterns.md](references/patterns.md)
