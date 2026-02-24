# Advanced Rate Limiter Features

## Table of Contents

- [Sharding](#sharding)
- [Capacity Reservation](#capacity-reservation)
- [Direct Value Access](#direct-value-access)
- [Jitter](#jitter)
- [Troubleshooting](#troubleshooting)
- [Performance Characteristics](#performance-characteristics)

---

## Sharding

**Problem:** High contention on rate limit values causes OCC (Optimistic Concurrency Control) conflicts at high QPS.

**Solution:** Split capacity across multiple shards.

```typescript
const rateLimiter = new RateLimiter(components.rateLimiter, {
  // Without sharding — may face contention at high QPS
  basicLimit: { kind: "fixed window", rate: 1000, period: MINUTE },

  // With sharding — handles high throughput
  scalableLimit: { kind: "fixed window", rate: 1000, period: MINUTE, shards: 10 },

  // LLM token limit with sharding
  llmTokens: { kind: "token bucket", rate: 40000, period: MINUTE, shards: 10 },
});
```

**How it works:**
- Capacity is divided across N shards (e.g., 10 shards = 100 capacity each)
- Each request checks 2 random shards (power-of-two technique)
- Uses the shard with more capacity; combines both if neither has enough alone
- Never violates total rate limit

**Guidelines:**

| Rule | Detail |
|---|---|
| When to shard | Only when seeing OCC conflicts at high QPS |
| Shard count | `shards ≈ max_qps / 2` |
| Min capacity per shard | 5–10+ tokens each |
| Tradeoff | May occasionally reject when capacity exists elsewhere |

**Scaling fixed periods for effective sharding:**

```typescript
// BAD: 100/second with 50 shards → only 2 capacity per shard
{ rate: 100, period: SECOND, shards: 50 }

// GOOD: Scale rate and period proportionally
{ rate: 1000, period: 10 * SECOND, shards: 50 }
// Now each shard has 20 capacity
```

```typescript
// BAD: Over-sharding — each shard has only 1 capacity
{ rate: 100, period: MINUTE, shards: 100 }

// GOOD: Balanced shards
{ rate: 100, period: MINUTE, shards: 10 }
// Each shard has 10 capacity
```

---

## Capacity Reservation

**Problem:** Large requests get starved by smaller ones.

**Solution:** Reserve capacity ahead of time for guaranteed execution.

```typescript
export const processLargeRequest = internalAction({
  args: { data: v.any(), skipCheck: v.optional(v.boolean()) },
  handler: async (ctx, args) => {
    if (!args.skipCheck) {
      const status = await rateLimiter.limit(ctx, "llmRequests", {
        count: 50,
        reserve: true,
      });

      if (status.retryAfter) {
        // Schedule for later with reserved capacity
        return ctx.scheduler.runAfter(
          status.retryAfter,
          internal.foo.processLargeRequest,
          { data: args.data, skipCheck: true },
        );
      }
    }

    // Execute with guaranteed capacity
    await performOperation(args.data);
  },
});
```

**How reservation works:**
1. Request reserves future capacity instead of failing
2. Returns `retryAfter` timestamp when capacity will be available
3. Schedule the operation for that time
4. When executed later, skip the rate limit check (`skipCheck: true`)
5. Prevents starvation and maximizes rate limit utilization

```typescript
// BAD: Large request might starve
await rateLimiter.limit(ctx, "llmTokens", { count: 5000 });

// GOOD: Reserve capacity for large request
await rateLimiter.limit(ctx, "llmTokens", { count: 5000, reserve: true });
```

---

## Direct Value Access

### Get current rate limit state

```typescript
export const getRateLimitValue = query({
  args: { userId: v.string() },
  handler: async (ctx, args) => {
    const { config, value, ts } = await rateLimiter.getValue(
      ctx, "sendMessage", { key: args.userId }
    );
    return { currentCapacity: value, lastUpdated: ts, configuration: config };
  },
});
```

### Calculate rate limit at a future time

```typescript
import { calculateRateLimit } from "@convex-dev/rate-limiter";

const { config, value, ts } = await rateLimiter.getValue(ctx, "sendMessage", {
  key: userId,
});

const futureState = calculateRateLimit(
  { value, ts },
  config,
  Date.now() + 60000, // 1 minute from now
  0,                   // count to consume
);
// futureState.value = projected capacity
```

---

## Jitter

**Problem:** Many clients retrying at the exact same time causes thundering herd.

**Solution:** Add randomness to retry times.

```typescript
const status = await rateLimiter.limit(ctx, "apiCalls");

if (!status.ok && status.retryAfter) {
  const jitter = Math.random() * MINUTE;
  const retryWithJitter = status.retryAfter + jitter;
  throw new Error(`Retry after ${retryWithJitter}ms`);
}
```

**Built-in jitter:** Fixed window strategy uses a random start time by default, preventing all clients from hitting the reset boundary simultaneously. Override with a custom `start` timestamp only if you need aligned windows.

```typescript
// BAD: All clients retry at exact same time
const { retryAfter } = await rateLimiter.limit(ctx, "action");
await sleep(retryAfter);

// GOOD: Add jitter
const { retryAfter } = await rateLimiter.limit(ctx, "action");
await sleep(retryAfter + Math.random() * MINUTE);
```

---

## Troubleshooting

| Issue | Solution |
|---|---|
| **OCC conflicts** | Add sharding; start with 5–10 shards, increase gradually |
| **Rate limits too strict** | Increase `capacity` for burst allowance; switch to token bucket |
| **Rate limits too lenient** | Decrease `capacity`; switch to fixed window for harder limits |
| **Users report unfair limits** | Use `reserve: true` for large requests; add jitter; use per-user keys |
| **Rate limiter not resetting** | Verify correct `key`; check period config; call `reset()` manually |

---

## Performance Characteristics

| Property | Detail |
|---|---|
| **Storage** | Not proportional to requests — only stores current value + timestamp per key |
| **Compute** | O(1) per check; no scans or aggregations |
| **Transactional** | All changes roll back if mutation fails; never lose track of consumed capacity |
| **Scalability** | Sharding for high QPS; separate instances per service/feature for horizontal scale |
