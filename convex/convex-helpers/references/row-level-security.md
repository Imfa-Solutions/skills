# Row-Level Security (RLS)

Import: `"convex-helpers/server/rowLevelSecurity"`

Add per-document access checks to all database operations. Any `ctx.db` access in wrapped functions runs your rules.

## 1. Define rules

```ts
import { Rules, RLSConfig, wrapDatabaseReader, wrapDatabaseWriter } from "convex-helpers/server/rowLevelSecurity";
import { DataModel } from "./_generated/dataModel";
import { QueryCtx } from "./_generated/server";

async function rlsRules(ctx: QueryCtx) {
  const identity = await ctx.auth.getUserIdentity();

  return {
    users: {
      read: async (_, user) => {
        if (!identity && user.age < 18) return false;
        return true;
      },
      insert: async (_, user) => true,
      modify: async (_, user) => {
        if (!identity) throw new Error("Must be authenticated");
        return user.tokenIdentifier === identity.tokenIdentifier;
      },
    },
    posts: {
      read: async (_, post) => {
        if (post.published) return true;
        return identity?.tokenIdentifier === post.authorToken;
      },
      modify: async (_, post) => identity?.tokenIdentifier === post.authorToken,
    },
  } satisfies Rules<QueryCtx, DataModel>;
}
```

## 2. Create wrapped functions

```ts
import { customQuery, customMutation, customCtx } from "convex-helpers/server/customFunctions";
import { query, mutation } from "./_generated/server";

const config: RLSConfig = { defaultPolicy: "deny" };

const queryWithRLS = customQuery(
  query,
  customCtx(async (ctx) => ({
    db: wrapDatabaseReader(ctx, ctx.db, await rlsRules(ctx), config),
  }))
);

const mutationWithRLS = customMutation(
  mutation,
  customCtx(async (ctx) => ({
    db: wrapDatabaseWriter(ctx, ctx.db, await rlsRules(ctx), config),
  }))
);
```

## 3. Use wrapped functions

```ts
export const getUser = queryWithRLS({
  args: { id: v.id("users") },
  handler: async (ctx, { id }) => {
    return await ctx.db.get(id);  // Returns null if read rule returns false
  },
});

export const updateUser = mutationWithRLS({
  args: { id: v.id("users"), patch: v.any() },
  handler: async (ctx, { id, patch }) => {
    await ctx.db.patch(id, patch);  // Throws if modify rule returns false
  },
});
```

## Rules type

```ts
type Rules<Ctx, DataModel> = {
  [TableName in TableNamesInDataModel<DataModel>]?: {
    read?: (ctx: Ctx, doc: Document) => Promise<boolean>;
    modify?: (ctx: Ctx, doc: Document) => Promise<boolean>;
    insert?: (ctx: Ctx, value: NewDocument) => Promise<boolean>;
  };
};
```

- `defaultPolicy: "allow"` — permit access when no rule defined for a table
- `defaultPolicy: "deny"` — deny access when no rule defined (recommended)
