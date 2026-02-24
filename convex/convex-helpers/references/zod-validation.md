# Zod Validation

Import (Zod v4): `"convex-helpers/server/zod4"` | Import (Zod v3): `"convex-helpers/server/zod"`

Use Zod schemas for argument validation instead of Convex `v` validators.

## Setup

```ts
import * as z from "zod";
import { zCustomQuery, zCustomMutation, zid } from "convex-helpers/server/zod4";
import { NoOp } from "convex-helpers/server/customFunctions";

const zodQuery = zCustomQuery(query, NoOp);
const zodMutation = zCustomMutation(mutation, NoOp);
```

## Usage

```ts
export const myQuery = zodQuery({
  args: {
    userId: zid("users"),
    email: z.email(),
    num: z.number().min(0),
    nullableBigint: z.nullable(z.bigint()),
    boolWithDefault: z.boolean().default(true),
    null: z.null(),
    array: z.array(z.string()),
    optionalObject: z.object({ a: z.string(), b: z.number() }).optional(),
    union: z.union([z.string(), z.number()]),
    discriminatedUnion: z.discriminatedUnion("kind", [
      z.object({ kind: z.literal("a"), a: z.string() }),
      z.object({ kind: z.literal("b"), b: z.number() }),
    ]),
    literal: z.literal("hi"),
    enum: z.enum(["a", "b"]),
    readonly: z.object({ a: z.string() }).readonly(),
    pipeline: z.number().pipe(z.coerce.string()),
  },
  handler: async (ctx, args) => {
    // args fully typed from Zod schema
  },
});
```

## With custom functions

```ts
const zodQueryWithUser = zCustomQuery(queryWithUser, NoOp);

export const getUserData = zodQueryWithUser({
  args: {
    userId: zid("users"),
    includePosts: z.boolean().default(false),
  },
  handler: async (ctx, args) => {
    // ctx.user from queryWithUser + Zod-validated args
  },
});
```

## zid helper

Creates a Zod validator for Convex document IDs.

```ts
import { zid } from "convex-helpers/server/zod4";

const userId = zid("users");  // ZodType<Id<"users">>

const schema = z.object({
  authorId: zid("users"),
  postId: zid("posts"),
});
```

## Supported Zod types

`z.string()`, `z.number()`, `z.boolean()`, `z.bigint()`, `z.null()`, `z.undefined()`, `z.any()`, `z.unknown()`, `z.literal()`, `z.enum()`, `z.nativeEnum()`, `z.object()`, `z.array()`, `z.record()`, `z.union()`, `z.discriminatedUnion()`, `z.intersection()`, `z.optional()`, `z.nullable()`, `z.default()`, `z.pipe()`, `z.readonly()`, `z.custom()`
