# Common Migration Patterns & Testing

## Table of Contents

1. [Adding a Required Field](#adding-a-required-field)
2. [Renaming a Field](#renaming-a-field)
3. [Converting Field Types](#converting-field-types)
4. [Data Denormalization](#data-denormalization)
5. [Deployment Integration](#deployment-integration)
6. [Testing with convex-test](#testing-with-convex-test)
7. [Testing Best Practices](#testing-best-practices)

---

## Adding a Required Field

Three-phase approach: optional → migrate → required.

### Phase 1: Schema — add as optional

```typescript
export default defineSchema({
  users: defineTable({
    name: v.string(),
    email: v.optional(v.string()), // new field, optional initially
  }),
});
```

### Phase 2: Migration — backfill existing documents

```typescript
export const backfillEmail = migrations.define({
  table: "users",
  migrateOne: async (ctx, user) => {
    if (user.email === undefined) {
      const email = await generateEmail(user.name);
      return { email };
    }
  },
});
```

### Phase 3: After migration completes — make required

```typescript
export default defineSchema({
  users: defineTable({
    name: v.string(),
    email: v.string(), // now required
  }),
});
```

---

## Renaming a Field

Keep both fields temporarily, copy old → new, then remove old.

### Schema — both fields optional during transition

```typescript
export default defineSchema({
  posts: defineTable({
    authorId: v.optional(v.id("users")),  // new name
    userId: v.optional(v.id("users")),    // old name (deprecated)
  }),
});
```

### Migration — copy and clear

```typescript
export const renameAuthorField = migrations.define({
  table: "posts",
  migrateOne: async (ctx, post) => {
    if (post.userId !== undefined && post.authorId === undefined) {
      return {
        authorId: post.userId,
        userId: undefined, // clear old field
      };
    }
  },
});
```

### After migration — remove old field from schema

```typescript
export default defineSchema({
  posts: defineTable({
    authorId: v.id("users"), // required, old field gone
  }),
});
```

---

## Converting Field Types

Example: convert a string date to a numeric timestamp.

```typescript
export const convertDateField = migrations.define({
  table: "events",
  migrateOne: async (ctx, event) => {
    if (typeof event.date === "string") {
      return {
        date: new Date(event.date).getTime(),
      };
    }
  },
});
```

During the transition, declare the field as a union in your schema:

```typescript
defineTable({
  date: v.union(v.string(), v.float64()), // accepts both during migration
})
```

After migration completes, narrow to the final type:

```typescript
defineTable({
  date: v.float64(), // only timestamp now
})
```

---

## Data Denormalization

Add a computed/cached field derived from related documents.

```typescript
export const addCommentCount = migrations.define({
  table: "posts",
  migrateOne: async (ctx, post) => {
    if (post.commentCount === undefined) {
      const count = await ctx.db
        .query("comments")
        .withIndex("by_post", (q) => q.eq("postId", post._id))
        .collect()
        .then((c) => c.length);
      return { commentCount: count };
    }
  },
});
```

After migration, keep the denormalized field in sync by updating it in the mutations that create/delete comments.

---

## Deployment Integration

Run migrations automatically as part of your deploy pipeline:

```json
{
  "scripts": {
    "deploy": "npx convex deploy --cmd 'npm run build' && npx convex run migrations:runAll --prod"
  }
}
```

---

## Testing with convex-test

Use `runToCompletion` to run migrations synchronously inside test functions.

```typescript
import { test, expect } from "vitest";
import { convexTest } from "convex-test";
import component from "@convex-dev/migrations/test";
import { runToCompletion } from "@convex-dev/migrations";
import { components, internal } from "./_generated/api";
import schema from "./schema";

test("setDefaultValue migration", async () => {
  const t = convexTest(schema);
  component.register(t);

  await t.run(async (ctx) => {
    // Setup test data — include both migrated and unmigrated docs
    await ctx.db.insert("myTable", { optionalField: undefined });
    await ctx.db.insert("myTable", { optionalField: "existing" });

    // Run migration synchronously
    await runToCompletion(
      ctx,
      components.migrations,
      internal.migrations.setDefaultValue
    );

    // Verify results
    const docs = await ctx.db.query("myTable").collect();
    expect(docs[0].optionalField).toBe("default");
    expect(docs[1].optionalField).toBe("existing");
  });
});
```

### Key testing imports

```typescript
import component from "@convex-dev/migrations/test";   // test helper
import { runToCompletion } from "@convex-dev/migrations"; // sync runner
```

`component.register(t)` wires the migrations component into the test harness. Without it, `runToCompletion` won't find the component tables.

---

## Testing Best Practices

- **Cover both paths** — include documents that need migration and documents that are already correct.
- **Verify idempotency** — run the migration twice in the same test and assert no errors or double-writes.
- **Realistic volume** — test with enough documents to exercise batch boundaries (e.g., if `batchSize` is 10, insert 25 documents).
- **Schema validation** — after migration, verify that every document would pass the final (required) schema.
