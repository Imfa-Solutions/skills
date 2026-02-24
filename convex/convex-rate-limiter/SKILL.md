---
name: convex-rate-limiter
description: >
  Convex Rate Limiter component (@convex-dev/rate-limiter) — type-safe, transactional rate limiting
  with token bucket and fixed window strategies. Use when implementing rate limiting in a Convex app,
  configuring per-user or global rate limits, protecting mutations/actions from abuse, handling
  failed login throttling, LLM token/request quotas, or signup caps. Triggers on: rate limit,
  rate-limiter, RateLimiter, token bucket, fixed window, @convex-dev/rate-limiter, throttle requests,
  abuse prevention, quota management, sharding rate limits, capacity reservation. Also use when the
  user asks "how do I rate limit in Convex", "add rate limiting to my mutation", "limit API calls
  per user", or wants to review/fix existing rate limiter code.
---

# Convex Rate Limiter

> `@convex-dev/rate-limiter` — Type-safe, transactional, application-level rate limiting for Convex.

## Installation

```bash
npm install @convex-dev/rate-limiter
```

Register the component in `convex/convex.config.ts`:

```typescript
import { defineApp } from "convex/server";
import rateLimiter from "@convex-dev/rate-limiter/convex.config.js";

const app = defineApp();
app.use(rateLimiter);
export default app;
```

## Setup — Define Named Rate Limits

```typescript
import { RateLimiter, MINUTE, HOUR, SECOND } from "@convex-dev/rate-limiter";
import { components } from "./_generated/api";

const rateLimiter = new RateLimiter(components.rateLimiter, {
  // Fixed window — hard quota that resets each period
  freeTrialSignUp: { kind: "fixed window", rate: 100, period: HOUR },

  // Token bucket — smooth traffic with burst allowance
  sendMessage: { kind: "token bucket", rate: 10, period: MINUTE, capacity: 3 },

  // Failed login throttle
  failedLogins: { kind: "token bucket", rate: 10, period: HOUR },
});
```

- `period` is in milliseconds (`SECOND = 1000`, `MINUTE = 60000`, `HOUR = 3600000`).
- Multiple `RateLimiter` instances allowed; config keys must not overlap.

## Strategy Selection

| Strategy | Behavior | Best for |
|---|---|---|
| **Token bucket** | Tokens refill continuously; unused tokens accumulate up to `capacity` | User actions, API calls, LLM tokens — smooth traffic, allow bursts |
| **Fixed window** | All tokens granted at period start; hard reset each period | Daily/hourly quotas, signup caps, hard API limits |

**Token bucket config:**
```typescript
{ kind: "token bucket", rate: 10, period: MINUTE, capacity: 3 }
// capacity = max burst tokens (optional, defaults to rate)
```

**Fixed window config:**
```typescript
{ kind: "fixed window", rate: 100, period: HOUR }
// start: optional custom epoch; random by default to prevent thundering herd
```

## Core API

### `limit()` — Consume tokens

```typescript
const { ok, retryAfter } = await rateLimiter.limit(ctx, "sendMessage", {
  key: userId,       // Per-entity key (omit for global limit)
  count: 1,          // Tokens to consume (default 1)
  throws: false,     // Set true to auto-throw ConvexError
});
if (!ok) throw new Error(`Rate limited. Retry in ${Math.ceil(retryAfter / 1000)}s`);
```

### `check()` — Query without consuming (safe in queries)

```typescript
const { ok, retryAfter } = await rateLimiter.check(ctx, "sendMessage", {
  key: userId,
});
```

### `reset()` — Clear a rate limit

```typescript
await rateLimiter.reset(ctx, "failedLogins", { key: email });
```

### `throws: true` — Auto-throw pattern

```typescript
import { isRateLimitError } from "@convex-dev/rate-limiter";

await rateLimiter.limit(ctx, "sendMessage", { key: userId, throws: true });
// Throws ConvexError with data: { kind, name, retryAfter }
```

Catch with `isRateLimitError(error)` to inspect `.data.retryAfter`.

## Usage Patterns

### Global rate limit (no key)

```typescript
export const signUp = mutation({
  args: { email: v.string() },
  handler: async (ctx, args) => {
    await rateLimiter.limit(ctx, "freeTrialSignUp", { throws: true });
    await ctx.db.insert("users", { email: args.email });
  },
});
```

### Per-user rate limit

```typescript
export const sendMessage = mutation({
  args: { text: v.string() },
  handler: async (ctx, args) => {
    const user = await ctx.auth.getUserIdentity();
    await rateLimiter.limit(ctx, "sendMessage", {
      key: user?.subject,
      throws: true,
    });
    await ctx.db.insert("messages", { userId: user?.subject, text: args.text });
  },
});
```

### Failed login throttle with reset on success

```typescript
export const login = mutation({
  args: { email: v.string(), password: v.string() },
  handler: async (ctx, args) => {
    await rateLimiter.limit(ctx, "failedLogins", {
      key: args.email,
      throws: true,
    });
    const success = await verifyCredentials(ctx, args);
    if (success) {
      await rateLimiter.reset(ctx, "failedLogins", { key: args.email });
    }
    return success;
  },
});
```

### Consume multiple tokens at once

```typescript
await rateLimiter.limit(ctx, "llmTokens", {
  key: userId,
  count: estimateTokens(prompt),
  throws: true,
});
```

## Critical Rules

1. **Always pass `key`** for per-entity limits. Omitting `key` makes it a global singleton.
2. **Always surface `retryAfter`** to the client — don't just say "rate limited".
3. **Rate limit changes are transactional** — they roll back if the mutation fails.
4. **Use `throws: true`** for cleaner code; catch with `isRateLimitError()`.
5. **Reset wisely** — reset on success (e.g., login), never on every request.

## Common Pitfalls

```typescript
// BAD: Global limit when you want per-user
await rateLimiter.limit(ctx, "sendMessage");

// GOOD: Per-user limit
await rateLimiter.limit(ctx, "sendMessage", { key: userId });
```

```typescript
// BAD: Ignoring retryAfter
const { ok } = await rateLimiter.limit(ctx, "action");
if (!ok) throw new Error("Rate limited");

// GOOD: Tell user when to retry
const { ok, retryAfter } = await rateLimiter.limit(ctx, "action");
if (!ok) throw new Error(`Wait ${Math.ceil(retryAfter / 1000)} seconds`);
```

## Best Practices Summary

| Practice | Guidance |
|---|---|
| **Key design** | User ID for per-user, IP for anonymous, team ID for team-wide, composites like `${teamId}:${userId}` |
| **Capacity** | Token bucket: `capacity = rate * burst_multiplier`. Fixed window: defaults to `rate` |
| **Start simple** | Add sharding/reservation only when needed |
| **Error handling** | Use `throws: true` + `isRateLimitError()` |
| **Thundering herd** | Fixed window uses random start by default; add jitter to retry times |

## Advanced Topics

- **Sharding for high throughput (OCC conflicts)**: See [references/advanced.md](references/advanced.md#sharding)
- **Capacity reservation (prevent starvation)**: See [references/advanced.md](references/advanced.md#capacity-reservation)
- **Direct value access & `calculateRateLimit`**: See [references/advanced.md](references/advanced.md#direct-value-access)
- **Jitter patterns**: See [references/advanced.md](references/advanced.md#jitter)
- **Troubleshooting**: See [references/advanced.md](references/advanced.md#troubleshooting)

## Common Patterns

- **Tiered rate limits (free vs premium)**: See [references/patterns.md](references/patterns.md#tiered-rate-limits)
- **Anonymous/IP-based limiting**: See [references/patterns.md](references/patterns.md#anonymous-and-ip-based-limiting)
- **Multiple rate limits in one transaction**: See [references/patterns.md](references/patterns.md#multiple-rate-limits-in-one-transaction)
- **React client integration (`useRateLimit`)**: See [references/patterns.md](references/patterns.md#react-client-integration)
- **Inline/dynamic config**: See [references/patterns.md](references/patterns.md#inline-dynamic-config)
- **Testing**: See [references/patterns.md](references/patterns.md#testing)
