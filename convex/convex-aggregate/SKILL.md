---
name: convex-aggregate
description: >
  Convex Aggregate component (@convex-dev/aggregate) — O(log n) count, sum, min, max, percentile,
  rank, and offset-based pagination using a denormalized B-tree. Covers TableAggregate (table-bound)
  and DirectAggregate (standalone), key design, namespaces, bounds/prefix queries, batch operations,
  triggers (convex-helpers), backfill migrations, contention optimization, and reactive aggregation.
  Use when implementing leaderboards, rankings, offset pagination, random access, analytics
  (mean/median/p95/p99), counting documents, per-user aggregations, or efficient sum/count in Convex.
  Triggers on: @convex-dev/aggregate, TableAggregate, DirectAggregate, aggregate.count, aggregate.sum,
  aggregate.at, aggregate.indexOf, aggregate.min, aggregate.max, aggregate.random, sortKey, sumValue,
  namespace, leaderboard, offset pagination, percentile, rank, "how do I count efficiently in Convex",
  "aggregate component", "O(log n) count".
---

# Convex Aggregate — O(log n) Count, Sum, Rank & Pagination

> `@convex-dev/aggregate` — Efficient aggregation via denormalized B-tree. O(log n) for count, sum, min, max, rank, offset access, and percentiles.

## Installation & Setup

```bash
npm install @convex-dev/aggregate
```

```typescript
// convex/convex.config.ts
import { defineApp } from "convex/server";
import aggregate from "@convex-dev/aggregate/convex.config.js";

const app = defineApp();
app.use(aggregate);
// Multiple aggregates:
// app.use(aggregate, { name: "byScore" });
// app.use(aggregate, { name: "byUser" });
export default app;
```

Run `npx convex dev` to generate the component API.

## Core Concepts

### TableAggregate vs DirectAggregate

| | TableAggregate | DirectAggregate |
|---|---|---|
| **Tied to** | A Convex table | Nothing (standalone) |
| **Sync** | Derives keys from doc fields | Manual insert/delete/replace |
| **Best for** | Table data with auto-sync | Analytics, metrics, non-table data |
| **Constructor** | `sortKey`, `sumValue`, `namespace` fns | Just type params |

### Keys

Sort keys determine ordering. Can be: `number`, `string`, `null`, or **tuples** (`[string, number]`).

**Critical**: Sort order follows key structure:
```typescript
// Key: [game, score] → max({ prefix: [game] }) returns highest SCORE for that game
// Key: [game, username] → max({ prefix: [game] }) returns highest USERNAME, not score!
```

### Namespaces

Partition data into separate B-trees. Each namespace is isolated — no contention between them, but no cross-namespace aggregation.

Use when: data is naturally partitioned (games, albums, orgs) AND you don't need global aggregates.

### Bounds

Limit query range — reduces read dependencies and write contention:

```typescript
// Range
{ bounds: { lower: { key: 65, inclusive: false }, upper: { key: 100, inclusive: true } } }
// Prefix (for tuple keys)
{ bounds: { prefix: [gameId, username] } }
// Exact match
{ bounds: { eq: specificKey } }
```

## TableAggregate Setup

```typescript
import { TableAggregate } from "@convex-dev/aggregate";
import { components } from "./_generated/api";
import type { DataModel } from "./_generated/dataModel";

const aggregate = new TableAggregate<{
  Key: number;                    // Sort key type
  DataModel: DataModel;
  TableName: "scores";
  Namespace?: string;             // Optional
}>(components.aggregate, {
  sortKey: (doc) => doc.score,    // REQUIRED: extract sort key
  sumValue: (doc) => doc.score,   // Optional: value for sum()
  namespace: (doc) => doc.gameId, // Optional: partition key
});
```

## DirectAggregate Setup

```typescript
import { DirectAggregate } from "@convex-dev/aggregate";

const aggregate = new DirectAggregate<{
  Key: number;
  Id: string;
  Namespace?: string;
}>(components.aggregate);
```

## Query Methods (both TableAggregate & DirectAggregate)

```typescript
// Count (all or bounded)
await aggregate.count(ctx);
await aggregate.count(ctx, { bounds: { prefix: [gameId] }, namespace: "ns" });

// Sum (requires sumValue)
await aggregate.sum(ctx);
await aggregate.sum(ctx, { bounds: { lower: { key: 0, inclusive: true } } });

// Offset access (0-indexed, supports negative)
await aggregate.at(ctx, 0);       // first
await aggregate.at(ctx, -1);      // last
await aggregate.at(ctx, 99, { namespace: "album1" });

// Rank (how many items before this key)
await aggregate.indexOf(ctx, 95);
await aggregate.indexOf(ctx, score, { order: "desc" });

// Min / Max → { key, id, sumValue } | null
await aggregate.min(ctx, { bounds: { prefix: [gameId] } });
await aggregate.max(ctx, { namespace: "game1" });

// Random (uniform)
await aggregate.random(ctx);

// Paginate
const { page, cursor, isDone } = await aggregate.paginate(ctx, {
  cursor: undefined, order: "asc", pageSize: 100,
  bounds: { prefix: [gameId] },
});

// Async iterator
for await (const item of aggregate.iter(ctx, { order: "desc", pageSize: 50 })) {
  // item: { key, id, sumValue }
}
```

## Write Methods

### TableAggregate writes (call after db operations)

```typescript
// After db.insert
const id = await ctx.db.insert("scores", data);
const doc = await ctx.db.get(id);
await aggregate.insert(ctx, doc!);

// After db.delete
await aggregate.delete(ctx, doc);

// After db.patch / db.replace
await aggregate.replace(ctx, oldDoc, newDoc);

// Idempotent versions (for migrations/backfills):
await aggregate.insertIfDoesNotExist(ctx, doc);
await aggregate.deleteIfExists(ctx, doc);
await aggregate.replaceOrInsert(ctx, oldDoc, newDoc);

// Document ranking
const rank = await aggregate.indexOfDoc(ctx, doc, { order: "asc" });
```

### DirectAggregate writes

```typescript
await aggregate.insert(ctx, { key: 95, id: "unique-id", sumValue: 95 });
await aggregate.delete(ctx, { key: 95, id: "unique-id" });
await aggregate.replace(ctx, { key: 95, id: "unique-id" }, { key: 100, sumValue: 100 });
// Same idempotent variants available
```

### Clear / reinitialize

```typescript
await aggregate.clear(ctx);
await aggregate.clear(ctx, { maxNodeSize: 32, rootLazy: false, namespace: "ns" });
await aggregate.clearAll(ctx); // all namespaces
await aggregate.makeRootLazy(ctx); // convert eager root to lazy
```

## Keeping Data in Sync

**CRITICAL**: Always update the aggregate when modifying the source table.

### Approach 1: Encapsulated helpers (recommended)

```typescript
async function insertScore(ctx, args) {
  const id = await ctx.db.insert("scores", args);
  const doc = await ctx.db.get(id);
  await aggregate.insert(ctx, doc!);
  return id;
}
```

### Approach 2: Triggers (convex-helpers)

```typescript
import { Triggers } from "convex-helpers/server/triggers";
import { customCtx, customMutation } from "convex-helpers/server/customFunctions";

const triggers = new Triggers<DataModel>();
triggers.register("scores", aggregate.trigger());

const mutationWithTriggers = customMutation(rawMutation, customCtx(triggers.wrapDB));

export const addScore = mutationWithTriggers({
  handler: async (ctx, args) => {
    return await ctx.db.insert("scores", args); // aggregate updates via trigger
  },
});
```

## Key Design Rules

| Goal | Key design | Why |
|---|---|---|
| Highest score per game | `[gameId, score]` | `max({ prefix: [gameId] })` returns max score |
| User-specific stats | `[username, score]` | `prefix: [username]` filters to one user |
| Time-based queries | `_creationTime` | Natural ordering for ranges |
| Simple count / random | `null` | No ordering needed |

**Avoid**: `[game, username]` if you want max *score* — max returns highest *username* alphabetically.

## Best Practices Summary

| Practice | Rationale |
|---|---|
| Always use bounds when possible | Reduces read dependencies and write contention |
| Use namespaces for partitioned data | Eliminates cross-partition contention |
| Use batch operations for multiple queries | Significantly more efficient than individual calls |
| Use encapsulated helpers or triggers | Prevents aggregate from going out of sync |
| Use `insertIfDoesNotExist` during backfills | Idempotent — safe to rerun |
| Use lazy root (default) for write-heavy | Spreads writes across tree |
| Set `rootLazy: false` for read-heavy | Faster reads at cost of write contention |

## Reference Files

- **Full examples**: Leaderboard, offset pagination, random access, user aggregations, analytics → See [references/examples.md](references/examples.md)
- **Advanced topics**: Batch ops, performance/contention optimization, troubleshooting, migrations, type definitions → See [references/advanced.md](references/advanced.md)
