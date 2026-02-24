# Performance Optimization

## Table of Contents

- [Core Principles](#core-principles)
- [Index Design](#index-design)
- [Denormalization Patterns](#denormalization-patterns)
- [N+1 Query Prevention](#n1-query-prevention)
- [OCC & Hot Spots](#occ--hot-spots)
- [Query Optimization](#query-optimization)
- [Transaction Boundaries](#transaction-boundaries)
- [Aggregate Component](#aggregate-component)

---

## Core Principles

1. **Queries should be O(log n)** — use indexes, never scan tables.
2. **Denormalize aggressively** — Convex has no joins.
3. **Minimize document reads** — each read in a query creates a reactive dependency.
4. **Avoid hot spots** — single documents with frequent writes cause OCC conflicts.

---

## Index Design

### Compound index strategy

Compound indexes are prefix-searchable. One index can serve multiple query patterns:

```typescript
.index("by_channel_author_deleted", ["channelId", "authorId", "isDeleted"])
// Serves:
// 1. .withIndex(..., q => q.eq("channelId", id))
// 2. .withIndex(..., q => q.eq("channelId", id).eq("authorId", id))
// 3. .withIndex(..., q => q.eq("channelId", id).eq("authorId", id).eq("isDeleted", false))
// Cannot skip fields: q.eq("channelId", id).eq("isDeleted", false) ← WRONG
```

**Remove redundant indexes:**
```typescript
// BAD: by_channel is redundant when by_channel_and_author exists
.index("by_channel", ["channelId"])
.index("by_channel_and_author", ["channelId", "authorId"])

// GOOD: single compound index
.index("by_channel_and_author", ["channelId", "authorId"])
```

### Index usage patterns

```typescript
// Equality on all fields
.withIndex("by_a_b_c", (q) => q.eq("a", 1).eq("b", 2).eq("c", 3))

// Prefix match
.withIndex("by_a_b_c", (q) => q.eq("a", 1).eq("b", 2))

// Range on last queried field
.withIndex("by_a_b_c", (q) => q.eq("a", 1).eq("b", 2).gt("c", 0))
```

### Never use `.filter()`

```typescript
// BAD: full table scan
const active = await ctx.db.query("users")
  .filter((q) => q.eq(q.field("status"), "active")).collect();

// GOOD: index
const active = await ctx.db.query("users")
  .withIndex("by_status", (q) => q.eq("status", "active")).collect();
```

`.filter()` is acceptable ONLY after narrowing with an index on a small result set.

---

## Denormalization Patterns

### Denormalized counts

```typescript
// BAD: collecting to count
const messages = await ctx.db.query("messages")
  .withIndex("by_channel", (q) => q.eq("channelId", channelId)).collect();
const count = messages.length; // unbounded read

// OK: "99+" pattern
const batch = await ctx.db.query("messages")
  .withIndex("by_channel", (q) => q.eq("channelId", channelId)).take(100);
const display = batch.length === 100 ? "99+" : String(batch.length);

// BEST: counter table
// Maintain channelStats.messageCount, update in same mutation as insert
```

### Denormalized boolean fields

Compute on write, index for O(log n) reads:

```typescript
// Schema
posts: defineTable({ body: v.string(), tags: v.array(v.string()), isImportant: v.boolean() })
  .index("by_important", ["isImportant"]),

// Mutation: compute on write
await ctx.db.insert("posts", {
  body, tags, isImportant: tags.includes("important"),
});

// Query: O(log n)
await ctx.db.query("posts").withIndex("by_important", (q) => q.eq("isImportant", true)).collect();
```

### Embed related data

Instead of N lookups, denormalize frequently-read related data:

```typescript
// BAD: store memberIds, look up each one
// GOOD: store members: [{ userId, name, avatar }] directly in team doc
```

---

## N+1 Query Prevention

```typescript
// BAD: N+1 — separate query per post
const posts = await ctx.db.query("posts").take(10);
const postsWithAuthors = await Promise.all(
  posts.map(async (p) => ({ ...p, author: await ctx.db.get(p.authorId) }))
);

// GOOD: batch fetch with deduplication
const posts = await ctx.db.query("posts").take(10);
const authorIds = [...new Set(posts.map((p) => p.authorId))];
const authors = await Promise.all(authorIds.map((id) => ctx.db.get(id)));
const authorMap = new Map(authors.map((a) => [a?._id, a]));
const postsWithAuthors = posts.map((p) => ({ ...p, author: authorMap.get(p.authorId) }));
```

---

## OCC & Hot Spots

Convex uses Optimistic Concurrency Control. Two mutations reading and writing the same document simultaneously → one retries automatically.

### Problem: hot counter

```typescript
// BAD: 100 concurrent clicks → 99 retries → cascading OCC errors
const counter = await ctx.db.query("counters").unique();
await ctx.db.patch(counter!._id, { count: counter!.count + 1 });
```

### Solution 1: Sharding

```typescript
// Write: pick random shard
const shardId = Math.floor(Math.random() * 10);
await ctx.db.insert("counterShards", { shardId, delta: 1 });

// Read: sum all shards
const shards = await ctx.db.query("counterShards").collect();
const total = shards.reduce((sum, s) => sum + s.delta, 0);
```

### Solution 2: Workpool (convex-helpers)

Serialize writes to avoid conflicts:

```typescript
import { Workpool } from "@convex-dev/workpool";
const pool = new Workpool(components.counterWorkpool, { maxParallelism: 1 });

await pool.enqueueMutation(ctx, internal.counters.doIncrement, {});
```

### Solution 3: Aggregate component

See [Aggregate Component](#aggregate-component) below.

### Best practices for OCC

- Make mutations **idempotent** — check current state before updating.
- **Patch directly** without reading first when possible.
- Use `Promise.all` for parallel independent updates.
- Avoid single-document global state.

---

## Query Optimization

### Use `take()` with bounded limits

```typescript
// BAD
const all = await ctx.db.query("messages").withIndex(...).collect();

// GOOD
const recent = await ctx.db.query("messages").withIndex(...).order("desc").take(50);
```

### Use `.first()` / `.unique()` instead of `.collect()[0]`

```typescript
const user = await ctx.db.query("users")
  .withIndex("by_email", (q) => q.eq("email", email)).unique();
```

### Parallel data fetching

```typescript
const [user, stats] = await Promise.all([
  ctx.db.get(userId),
  ctx.db.query("userStats").withIndex("by_user", (q) => q.eq("userId", userId)).unique(),
]);
```

---

## Transaction Boundaries

### Consolidate reads in actions

Multiple `ctx.runQuery` calls are NOT transactional — data can change between calls.

```typescript
// BAD: race condition between queries
const team = await ctx.runQuery(internal.teams.get, { teamId });
const owner = await ctx.runQuery(internal.users.get, { userId: team.ownerId });

// GOOD: single transactional query
const teamWithOwner = await ctx.runQuery(internal.teams.getTeamWithOwner, { teamId });
```

### Batch writes in mutations

```typescript
// BAD: multiple mutations in action — partial failure possible
for (const user of users) {
  await ctx.runMutation(internal.users.insert, { user });
}

// GOOD: single mutation with batch insert
export const createUsers = mutation({
  args: { users: v.array(v.object({ name: v.string() })) },
  returns: v.null(),
  handler: async (ctx, args) => {
    for (const user of args.users) {
      await ctx.db.insert("users", { name: user.name, createdAt: Date.now() });
    }
    return null; // all succeed or all fail together
  },
});
```

---

## Aggregate Component

For O(log n) count, sum, min, max, percentile on large tables.

### When to use

- Fast counts (total contributions, users per role)
- Sum/average calculations
- Offset-based pagination
- Rankings or percentiles
- Tables with 1000+ docs and frequent count queries

### Setup

```bash
npm install @convex-dev/aggregate
```

```typescript
// convex/convex.config.ts
import aggregate from "@convex-dev/aggregate/convex.config.js";
app.use(aggregate, { name: "contributionsByUser" });
```

```typescript
// convex/aggregates.ts
import { TableAggregate } from "@convex-dev/aggregate";
import { components } from "./_generated/api";
import type { DataModel } from "./_generated/dataModel";

export const contributionsByUser = new TableAggregate<{
  Namespace: string;
  Key: number;
  DataModel: DataModel;
  TableName: "wikiContributions";
}>(components.contributionsByUser, {
  namespace: (doc) => doc.authorId,
  sortKey: (doc) => doc.createdAt,
});
```

### Keep in sync

Every insert/delete/patch MUST update the aggregate:

```typescript
// Approach A: encapsulated helpers (recommended)
export async function insertContribution(ctx: MutationCtx, data: any) {
  const id = await ctx.db.insert("wikiContributions", data);
  const doc = await ctx.db.get(id);
  await contributionsByUser.insert(ctx, doc!);
  return id;
}

// Approach B: triggers (auto-sync)
import { Triggers } from "convex-helpers/server/triggers";
const triggers = new Triggers<DataModel>();
triggers.register("wikiContributions", contributionsByUser.trigger());
```

### Query

```typescript
// Count
const count = await contributionsByUser.count(ctx, { namespace: userId });

// Count with date range
const count = await contributionsByUser.count(ctx, {
  namespace: userId,
  bounds: { lower: { key: startDate, inclusive: true }, upper: { key: endDate, inclusive: true } },
});

// Min / Max
const earliest = await contributionsByUser.min(ctx, { namespace: userId });
const latest = await contributionsByUser.max(ctx, { namespace: userId });
```

### Backfill existing data

```typescript
export const backfill = internalMutation({
  handler: async (ctx) => {
    await contributionsByUser.clear(ctx);
    const all = await ctx.db.query("wikiContributions").collect();
    for (const doc of all) {
      await contributionsByUser.insertIfDoesNotExist(ctx, doc);
    }
  },
});
```

For tables with 10k+ docs, use `@convex-dev/migrations` with batching instead.
