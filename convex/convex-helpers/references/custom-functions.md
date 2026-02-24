# Custom Functions

Import: `"convex-helpers/server/customFunctions"`

Build customized versions of `query`, `mutation`, and `action` that:
- Run authentication before the request
- Add data to `ctx` (e.g., current user)
- Replace `ctx.db` (e.g., with RLS-wrapped version)
- Consume arguments not passed to handler (e.g., API tokens)
- Execute finalization logic via `onSuccess`

## customQuery / customMutation / customAction

The customization object:

```ts
{
  args?: PropertyValidators,       // Extra args consumed by input
  input: async (ctx, args, extra?) => ({
    ctx: { ...ctx, ...additionalCtx },  // Added to handler's ctx
    args: { ...filteredArgs },           // Passed to handler's args
    onSuccess?: ({ args, result }) => void,  // Optional post-handler callback
  }),
}
```

### Basic example — API token auth

```ts
import { customQuery } from "convex-helpers/server/customFunctions";
import { query } from "./_generated/server";

const myQueryBuilder = customQuery(query, {
  args: { apiToken: v.id("api_tokens") },
  input: async (ctx, args) => {
    const apiUser = await getApiUser(ctx, args.apiToken);
    return { ctx: { ...ctx, apiUser }, args: {} };
  },
});

export const getSomeData = myQueryBuilder({
  args: { someArg: v.string() },
  handler: async (ctx, args) => {
    // ctx.apiUser available, args = { someArg }
  },
});
```

### With onSuccess callback

```ts
const myQueryBuilder = customQuery(query, {
  args: { apiToken: v.id("api_tokens") },
  input: async (ctx, args) => {
    const apiUser = await getApiUser(ctx, args.apiToken);
    return {
      ctx: { ...ctx, apiUser },
      args: {},
      onSuccess: ({ args, result }) => {
        console.log(`User ${apiUser.name} called with`, args, "got", result);
      },
    };
  },
});
```

### Role-based access with extra input

```ts
const myQueryBuilder = customQuery(query, {
  args: {},
  input: async (ctx, args, { role }: { role: "admin" | "user" }) => {
    const user = await getUser(ctx);
    if (role === "admin" && user?.role !== "admin") throw new Error("Not admin");
    if (role === "user" && !user) throw new Error("Must be logged in");
    return { ctx: { ...ctx, user }, args: {} };
  },
});

export const adminOnlyQuery = myQueryBuilder({
  role: "admin",
  args: { id: v.id("users") },
  handler: async (ctx, args) => { /* only admins */ },
});
```

## customCtx

Shorthand when you only need to extend ctx (no extra args).

```ts
import { customCtx } from "convex-helpers/server/customFunctions";

const queryWithUser = customQuery(query, customCtx(async (ctx) => {
  const user = await getUser(ctx);
  return { user };
}));

// Equivalent to:
customQuery(query, {
  input: async (ctx) => ({
    ctx: { ...ctx, user: await getUser(ctx) },
    args: {},
  }),
});
```

## NoOp

Use when you want Zod validation without other customization.

```ts
import { NoOp } from "convex-helpers/server/customFunctions";
const zodQuery = zCustomQuery(query, NoOp);
```
