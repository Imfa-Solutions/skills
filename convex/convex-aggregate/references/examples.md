# Convex Aggregate — Patterns & Examples

## Table of Contents

- [Leaderboard with Rankings](#leaderboard-with-rankings)
- [Offset-Based Pagination (Photo Gallery)](#offset-based-pagination-photo-gallery)
- [Random Access (Shuffle)](#random-access-shuffle)
- [User-Specific Aggregations](#user-specific-aggregations)
- [Analytics Without Table (DirectAggregate)](#analytics-without-table-directaggregate)
- [Average and Percentile Queries](#average-and-percentile-queries)

---

## Leaderboard with Rankings

```typescript
// convex/schema.ts
export default defineSchema({
  scores: defineTable({
    gameId: v.id("games"),
    username: v.string(),
    score: v.number(),
  }),
});

// convex/leaderboard.ts
import { TableAggregate } from "@convex-dev/aggregate";
import { components } from "./_generated/api";
import type { DataModel } from "./_generated/dataModel";

const byScore = new TableAggregate<{
  Key: number;
  DataModel: DataModel;
  TableName: "scores";
}>(components.byScore, {
  sortKey: (doc) => -doc.score, // Negative for descending order
  sumValue: (doc) => doc.score,
});

// Add score
export const addScore = mutation({
  args: { gameId: v.id("games"), username: v.string(), score: v.number() },
  handler: async (ctx, args) => {
    const id = await ctx.db.insert("scores", args);
    const doc = await ctx.db.get(id);
    await byScore.insert(ctx, doc!);
  },
});

// Total count
export const totalScores = query({
  handler: async (ctx) => await byScore.count(ctx),
});

// Score at rank N
export const scoreAtRank = query({
  args: { rank: v.number() },
  handler: async (ctx, { rank }) => {
    const item = await byScore.at(ctx, rank);
    return ctx.db.get(item.id);
  },
});

// Rank of a score
export const rankOfScore = query({
  args: { score: v.number() },
  handler: async (ctx, { score }) => {
    return await byScore.indexOf(ctx, -score);
  },
});
```

---

## Offset-Based Pagination (Photo Gallery)

```typescript
// convex/schema.ts
export default defineSchema({
  photos: defineTable({
    album: v.string(),
    url: v.string(),
  }).index("by_album_creation_time", ["album"]),
});

// convex/photos.ts
const photos = new TableAggregate<{
  Namespace: string;
  Key: number;
  DataModel: DataModel;
  TableName: "photos";
}>(components.photos, {
  namespace: (doc) => doc.album,
  sortKey: (doc) => doc._creationTime,
});

// O(log n) jump to any page
export const pageOfPhotos = query({
  args: { album: v.string(), offset: v.number(), numItems: v.number() },
  handler: async (ctx, { album, offset, numItems }) => {
    const { key: creationTime } = await photos.at(ctx, offset, {
      namespace: album,
    });
    return await ctx.db
      .query("photos")
      .withIndex("by_album_creation_time", (q) =>
        q.eq("album", album).gte("_creationTime", creationTime)
      )
      .take(numItems);
  },
});

// Total count for pagination UI
export const photoCount = query({
  args: { album: v.string() },
  handler: async (ctx, { album }) => {
    return await photos.count(ctx, { namespace: album });
  },
});
```

---

## Random Access (Shuffle)

```typescript
const music = new TableAggregate<{
  DataModel: DataModel;
  TableName: "music";
  Key: null; // No ordering needed
}>(components.music, {
  sortKey: () => null,
});

export const randomSong = query({
  handler: async (ctx) => {
    const item = await music.random(ctx);
    if (!item) return null;
    return await ctx.db.get(item.id);
  },
});

export const totalSongs = query({
  handler: async (ctx) => await music.count(ctx),
});
```

---

## User-Specific Aggregations

```typescript
// Tuple key: [username, score]
const byUser = new TableAggregate<{
  Key: [string, number];
  DataModel: DataModel;
  TableName: "scores";
}>(components.byUser, {
  sortKey: (doc) => [doc.username, doc.score],
  sumValue: (doc) => doc.score,
});

// User's high score
export const userHighScore = query({
  args: { username: v.string() },
  handler: async (ctx, { username }) => {
    const item = await byUser.max(ctx, {
      bounds: { prefix: [username] },
    });
    return item?.sumValue ?? null;
  },
});

// User's average score
export const userAverageScore = query({
  args: { username: v.string() },
  handler: async (ctx, { username }) => {
    const count = await byUser.count(ctx, { bounds: { prefix: [username] } });
    if (count === 0) return null;
    const sum = await byUser.sum(ctx, { bounds: { prefix: [username] } });
    return sum / count;
  },
});

// User's rank for a specific score
export const userRank = query({
  args: { username: v.string(), score: v.number() },
  handler: async (ctx, { username, score }) => {
    return await byUser.indexOf(ctx, [username, score], {
      bounds: { prefix: [username] },
    });
  },
});
```

---

## Analytics Without Table (DirectAggregate)

```typescript
import { DirectAggregate } from "@convex-dev/aggregate";

const latencies = new DirectAggregate<{
  Key: number;
  Id: string;
}>(components.latencies);

// Report a latency measurement
export const reportLatency = mutation({
  args: { latency: v.number() },
  handler: async (ctx, { latency }) => {
    await latencies.insert(ctx, {
      key: latency,
      id: new Date().toISOString(),
      sumValue: latency,
    });
  },
});

// Get full statistics
export const getStats = query({
  handler: async (ctx) => {
    const count = await latencies.count(ctx);
    if (count === 0) return null;
    return {
      count,
      mean: (await latencies.sum(ctx)) / count,
      median: (await latencies.at(ctx, Math.floor(count / 2))).key,
      p75: (await latencies.at(ctx, Math.floor(count * 0.75))).key,
      p95: (await latencies.at(ctx, Math.floor(count * 0.95))).key,
      min: (await latencies.min(ctx))!.key,
      max: (await latencies.max(ctx))!.key,
    };
  },
});
```

---

## Average and Percentile Queries

Reusable pattern for any aggregate with `sumValue`:

```typescript
// Average
export const averageScore = query({
  handler: async (ctx) => {
    const count = await aggregate.count(ctx);
    if (count === 0) return 0;
    return (await aggregate.sum(ctx)) / count;
  },
});

// Percentile (p95, p99, median, etc.)
export const percentile = query({
  args: { p: v.number() }, // 0.5 = median, 0.95 = p95, 0.99 = p99
  handler: async (ctx, { p }) => {
    const count = await aggregate.count(ctx);
    if (count === 0) return null;
    const index = Math.floor(count * p);
    const item = await aggregate.at(ctx, Math.min(index, count - 1));
    return item.key;
  },
});

// Count in range
export const countInRange = query({
  args: { min: v.number(), max: v.number() },
  handler: async (ctx, { min, max }) => {
    return await aggregate.count(ctx, {
      bounds: {
        lower: { key: min, inclusive: true },
        upper: { key: max, inclusive: true },
      },
    });
  },
});
```
