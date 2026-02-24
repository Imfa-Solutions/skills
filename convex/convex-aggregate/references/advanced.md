# Convex Aggregate — Advanced Topics

## Table of Contents

- [Batch Operations](#batch-operations)
- [Performance & Contention](#performance--contention)
- [Troubleshooting](#troubleshooting)
- [Migrations & Backfilling](#migrations--backfilling)
- [Type Definitions](#type-definitions)

---

## Batch Operations

Batch calls are significantly more efficient than multiple individual calls — use them whenever querying the same aggregate multiple times.

### countBatch

```typescript
const counts = await aggregate.countBatch(ctx, [
  { bounds: { prefix: [game1] } },
  { bounds: { prefix: [game2] } },
  { bounds: { prefix: [game3] } },
]);
// Returns: [count1, count2, count3]
```

### sumBatch

```typescript
const sums = await aggregate.sumBatch(ctx, [
  { bounds: { prefix: [game1] } },
  { bounds: { prefix: [game2] } },
]);
```

### atBatch

```typescript
const items = await aggregate.atBatch(ctx, [
  { offset: 0, bounds: { prefix: [game1] } },
  { offset: 10, bounds: { prefix: [game1] } },
  { offset: 20, bounds: { prefix: [game1] } },
]);
```

---

## Performance & Contention

### Understanding the B-tree

The aggregate maintains a denormalized B-tree with counts/sums at each node. Nearby keys share internal nodes, which means:

- **Reads**: Queries rerun when nearby data changes
- **Writes**: Concurrent writes to nearby keys cause OCC conflicts

### Contention patterns

| Key pattern | Contention | Why |
|---|---|---|
| Sequential (timestamps) | **High** | All writes hit same end of tree |
| Random/scattered | **Low** | Writes spread across tree |
| Same prefix | **Medium** | Shared internal nodes |

### Optimization strategies

**1. Use namespaces** — each namespace is a separate B-tree:

```typescript
const leaderboard = new TableAggregate<{
  Namespace: Id<"games">;
  Key: number;
  DataModel: DataModel;
  TableName: "scores";
}>(components.leaderboard, {
  namespace: (doc) => doc.gameId,
  sortKey: (doc) => doc.score,
});
```

**2. Increase maxNodeSize** — fewer levels, less contention per write:

```typescript
await aggregate.clear(ctx, { maxNodeSize: 64 }); // Default: 16
```

Trade-off: slightly more reads for count/sum.

**3. Use lazy root (default)** — spreads writes across the tree:

```typescript
await aggregate.clear(ctx, { rootLazy: true });  // Default, best for writes
await aggregate.clear(ctx, { rootLazy: false });  // Faster reads, more contention
```

**4. Use bounds** — limit read dependencies:

```typescript
// Only reruns when scores > 95 change
await aggregate.count(ctx, {
  bounds: { lower: { key: 95, inclusive: false } },
});
```

**5. Use batch operations** instead of multiple individual calls.

### When to optimize

| Situation | Action |
|---|---|
| Queries rerunning frequently | Add bounds, use namespaces |
| OCC conflicts on writes | Use namespaces, increase maxNodeSize, ensure lazy root |
| High write throughput needed | Use namespaces, increase maxNodeSize |
| Read-heavy, few writes | Set `rootLazy: false` |
| Small dataset (< 1000) | Don't optimize |

---

## Troubleshooting

### Aggregate out of sync

**Symptoms**: count/sum returns incorrect values, missing items in pagination.

**Causes**: Mutation updated table but not aggregate; direct `ctx.db` write bypassed aggregate.

**Solutions**:

1. Clear and backfill:
```typescript
await aggregate.clear(ctx);
// Then run backfill migration
```

2. Rename component (nuclear option):
```typescript
// convex.config.ts
app.use(aggregate, { name: "newAggregateName" });
```

3. Compare and repair:
```typescript
const tablePage = await ctx.db.query("mytable").paginate(opts);
const aggPage = await aggregate.paginate(ctx, opts);
// Compare and fix differences
```

### OCC conflicts

**Symptoms**: Mutations failing with optimistic concurrency control errors, high retry rates.

**Solutions**: Use namespaces, increase `maxNodeSize`, ensure lazy root, use bounds.

### Queries rerunning frequently

**Symptoms**: High function call usage, UI updating constantly.

**Solutions**: Use bounds to limit read dependencies, use namespaces, consider if reactivity is needed.

### Slow queries

**Symptoms**: count/sum taking too long.

**Solutions**: Set `rootLazy: false` for read-heavy workloads, check bounds usage, verify index usage in related table queries.

---

## Migrations & Backfilling

### Adding aggregate to an existing table

**Step 1**: Update writes to use idempotent methods:

```typescript
await aggregate.insertIfDoesNotExist(ctx, doc);
```

**Step 2**: Deploy the code change.

**Step 3**: Run backfill migration:

```typescript
import { Migrations } from "@convex-dev/migrations";

const migrations = new Migrations<DataModel>(components.migrations);

export const backfillScores = migrations.define({
  table: "scores",
  migrateOne: async (ctx, doc) => {
    await aggregate.insertIfDoesNotExist(ctx, doc);
  },
});

export const runBackfill = migrations.runner(internal.migrations.backfillScores);
```

**Step 4**: Run from CLI:

```bash
npx convex run migrations:runBackfill
```

**Step 5**: Switch back to regular methods after backfill completes:

```typescript
await aggregate.insert(ctx, doc); // non-idempotent, normal operation
```

### Idempotent trigger for backfills

```typescript
const triggers = new Triggers<DataModel>();

// During migration — safe to rerun
triggers.register("scores", aggregate.idempotentTrigger());

// After migration — switch to regular trigger
triggers.register("scores", aggregate.trigger());
```

---

## Type Definitions

### Core types

```typescript
// Key types for sorting
type Key = string | number | boolean | null | Key[];

// Item returned from queries
type Item<K extends Key, ID extends string> = {
  key: K;
  id: ID;
  sumValue: number;
};

// Bounds for limiting queries
type Bound<K extends Key, ID extends string> = {
  key: K;
  id?: ID;
  inclusive: boolean;
};

type Bounds<K extends Key, ID extends string> =
  | { lower?: Bound<K, ID>; upper?: Bound<K, ID> }
  | { prefix: unknown[] }  // For tuple keys
  | { eq: K };             // Exact match
```

### TableAggregate type params

```typescript
type TableAggregateType<
  K extends Key,
  DataModel extends GenericDataModel,
  TableName extends TableNamesInDataModel<DataModel>,
  Namespace extends ConvexValue | undefined = undefined,
> = {
  Key: K;
  DataModel: DataModel;
  TableName: TableName;
  Namespace?: Namespace;
};
```

### DirectAggregate type params

```typescript
type DirectAggregateType<
  K extends Key,
  ID extends string,
  Namespace extends ConvexValue | undefined = undefined,
> = {
  Key: K;
  Id: ID;
  Namespace?: Namespace;
};
```
