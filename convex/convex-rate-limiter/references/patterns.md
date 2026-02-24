# Common Rate Limiter Patterns

## Table of Contents

- [Tiered Rate Limits](#tiered-rate-limits)
- [Anonymous and IP-Based Limiting](#anonymous-and-ip-based-limiting)
- [Multiple Rate Limits in One Transaction](#multiple-rate-limits-in-one-transaction)
- [React Client Integration](#react-client-integration)
- [Inline Dynamic Config](#inline-dynamic-config)
- [Testing](#testing)

---

## Tiered Rate Limits

Different limits based on user plan/tier:

```typescript
const rateLimiter = new RateLimiter(components.rateLimiter, {
  freeActions: { kind: "token bucket", rate: 10, period: HOUR },
  premiumActions: { kind: "token bucket", rate: 100, period: HOUR },
});

export const tieredAction = mutation({
  args: { userId: v.string() },
  handler: async (ctx, args) => {
    const user = await ctx.db.get(args.userId);
    const limitName = user.isPremium ? "premiumActions" : "freeActions";

    await rateLimiter.limit(ctx, limitName, {
      key: args.userId,
      throws: true,
    });

    // Proceed with action
  },
});
```

---

## Anonymous and IP-Based Limiting

### Anonymous users with fallback key

```typescript
const rateLimiter = new RateLimiter(components.rateLimiter, {
  anonymousActions: { kind: "token bucket", rate: 20, period: HOUR },
});

export const publicAction = mutation({
  handler: async (ctx) => {
    const user = await ctx.auth.getUserIdentity();
    const key = user?.subject ?? "anonymous";

    await rateLimiter.limit(ctx, "anonymousActions", { key, throws: true });
  },
});
```

### IP-based limiting

```typescript
export const ipRateLimited = mutation({
  handler: async (ctx) => {
    const ipAddress = await getClientIP(ctx); // from headers or client

    await rateLimiter.limit(ctx, "ipLimit", { key: ipAddress, throws: true });
  },
});
```

---

## Multiple Rate Limits in One Transaction

Check multiple limits in a single mutation — all changes roll back if the mutation fails:

```typescript
export const complexAction = mutation({
  handler: async (ctx) => {
    const userId = await getUserId(ctx);

    // Check API call limit
    const apiStatus = await rateLimiter.limit(ctx, "apiCalls", { key: userId });
    if (!apiStatus.ok) throw new Error("API rate limit exceeded");

    // Check token limit
    const tokenStatus = await rateLimiter.limit(ctx, "llmTokens", {
      key: userId,
      count: 1000,
    });
    if (!tokenStatus.ok) throw new Error("Token limit exceeded");

    // Both passed — proceed. If mutation fails, both roll back.
  },
});
```

---

## React Client Integration

### Server-side: expose hook API

```typescript
// convex/messages.ts
export const { getRateLimit, getServerTime } = rateLimiter.hookAPI(
  "sendMessage",
  {
    key: async (ctx) => {
      const user = await ctx.auth.getUserIdentity();
      return user?.subject ?? "anonymous";
    },
  }
);
```

### Client-side: `useRateLimit` hook

```typescript
import { useRateLimit } from "@convex-dev/rate-limiter/react";
import { api } from "../convex/_generated/api";

function MessageForm() {
  const {
    status: { ok, retryAt },
    check,
  } = useRateLimit(api.messages.getRateLimit, {
    getServerTimeMutation: api.messages.getServerTime, // clock sync (recommended)
    count: 1,
  });

  const handleSubmit = () => {
    if (!ok) {
      alert(`Wait ${Math.ceil((retryAt - Date.now()) / 1000)} seconds`);
      return;
    }
    // Send message
  };

  return (
    <button onClick={handleSubmit} disabled={!ok}>
      Send {!ok && `(wait ${Math.ceil((retryAt - Date.now()) / 1000)}s)`}
    </button>
  );
}
```

**`check()` for point-in-time evaluation:**
```typescript
const { value, ts, config, ok, retryAt } = check(Date.now(), 1);
```

### Server-side: key from client (with validation)

```typescript
export const { getRateLimit, getServerTime } = rateLimiter.hookAPI(
  "sendMessage",
  {
    key: async (ctx, keyFromClient) => {
      await ensureUserCanUseKey(ctx, keyFromClient);
      return keyFromClient;
    },
  }
);
```

---

## Inline Dynamic Config

Define rate limit config on the fly instead of at initialization:

```typescript
export const customRateLimit = mutation({
  args: { userId: v.string() },
  handler: async (ctx, args) => {
    const config = {
      kind: "fixed window" as const,
      rate: 5,
      period: SECOND,
    };

    const status = await rateLimiter.limit(ctx, "customLimit", {
      config,
      key: args.userId,
    });

    if (!status.ok) throw new Error("Custom rate limit exceeded");
  },
});
```

---

## Testing

```typescript
export const testRateLimiter = internalMutation({
  handler: async (ctx) => {
    // First 3 requests succeed (capacity: 3)
    for (let i = 0; i < 3; i++) {
      const result = await rateLimiter.limit(ctx, "sendMessage", {
        key: "testUser",
        throws: true,
      });
      assert(result.ok);
    }

    // Fourth should fail
    let threw = false;
    try {
      await rateLimiter.limit(ctx, "sendMessage", {
        key: "testUser",
        throws: true,
      });
    } catch (e) {
      threw = true;
      assert(isRateLimitError(e));
    }
    assert(threw);

    // Reset and verify
    await rateLimiter.reset(ctx, "sendMessage", { key: "testUser" });
    const afterReset = await rateLimiter.check(ctx, "sendMessage", {
      key: "testUser",
    });
    assert(afterReset.ok);
  },
});
```
