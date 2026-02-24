---
name: convex-helpers
description: >
  Complete reference for the convex-helpers NPM library — utility functions, custom function builders,
  relationship helpers, row-level security, Zod validation, validators, CRUD generation, triggers,
  query streams, pagination, filtering, CORS, Hono HTTP endpoints, session tracking, query caching,
  and React hooks for Convex apps. Use when writing or reviewing Convex server/client code that uses
  or should use convex-helpers utilities. Triggers on: import from "convex-helpers", customQuery,
  customMutation, customAction, customCtx, asyncMap, pruneNull, nullThrows, getOneFrom, getManyFrom,
  getManyVia, wrapDatabaseReader, wrapDatabaseWriter, zCustomQuery, zid, filter(), stream(),
  mergedStream, getPage, paginator, Triggers, crud(), corsRouter, HttpRouterWithHono, SessionProvider,
  useSessionQuery, ConvexQueryCacheProvider, literals, nullable, partial, brandedString, typedV,
  makeUseQueryWithStatus, or any convex-helpers/* import path.
---

# convex-helpers

NPM: `convex-helpers` | Repo: https://github.com/get-convex/convex-helpers

## Installation

```bash
npm install convex-helpers
# Zod validation: npm install zod
# Hono endpoints: npm install hono
```

## Quick Import Reference

```ts
// Core utilities
import { asyncMap, pruneNull, nullThrows, pick, omit } from "convex-helpers";

// Custom function builders
import { customQuery, customMutation, customAction, customCtx, NoOp } from "convex-helpers/server/customFunctions";

// Relationship traversal
import { getOrThrow, getAll, getAllOrThrow, getOneFrom, getOneFromOrThrow, getManyFrom, getManyVia, getManyViaOrThrow } from "convex-helpers/server/relationships";

// Row-level security
import { wrapDatabaseReader, wrapDatabaseWriter } from "convex-helpers/server/rowLevelSecurity";

// Zod validation (v4)
import { zCustomQuery, zCustomMutation, zid } from "convex-helpers/server/zod4";

// Validator utilities
import { literals, nullable, partial, deprecated, brandedString, systemFields, withSystemFields, doc, typedV, validate } from "convex-helpers/validators";

// Filter, pagination, streams
import { filter } from "convex-helpers/server/filter";
import { getPage, paginator } from "convex-helpers/server/pagination";
import { stream, mergedStream } from "convex-helpers/server/stream";

// Triggers
import { Triggers } from "convex-helpers/server/triggers";

// CRUD
import { crud } from "convex-helpers/server/crud";

// CORS & Hono
import { corsRouter } from "convex-helpers/server/cors";
import { HonoWithConvex, HttpRouterWithHono } from "convex-helpers/server/hono";

// React hooks
import { makeUseQueryWithStatus } from "convex-helpers/react";
import { ConvexQueryCacheProvider, useQuery, usePaginatedQuery } from "convex-helpers/react/cache";
import { SessionProvider, useSessionQuery, useSessionMutation } from "convex-helpers/react/sessions";
```

## Helper Selection Guide

| Need | Helper | Reference |
|------|--------|-----------|
| Wrap query/mutation with auth, logging, ctx extensions | `customQuery` / `customMutation` / `customCtx` | [custom-functions.md](references/custom-functions.md) |
| Traverse 1:1, 1:N, M:N relationships | `getOneFrom`, `getManyFrom`, `getManyVia` | [relationships.md](references/relationships.md) |
| Row-level access control on reads/writes | `wrapDatabaseReader` / `wrapDatabaseWriter` | [row-level-security.md](references/row-level-security.md) |
| Zod schemas for Convex args | `zCustomQuery`, `zid` | [zod-validation.md](references/zod-validation.md) |
| Shorthand validators (`literals`, `partial`, etc.) | `literals`, `nullable`, `partial`, `brandedString`, `typedV` | [validators.md](references/validators.md) |
| JS/TS filter on queries (beyond `.filter()`) | `filter()` | [filter-pagination-streams.md](references/filter-pagination-streams.md) |
| Manual pagination / multiple paginations per query | `getPage`, `paginator` | [filter-pagination-streams.md](references/filter-pagination-streams.md) |
| UNION/JOIN-style query composition | `stream`, `mergedStream`, `flatMap` | [filter-pagination-streams.md](references/filter-pagination-streams.md) |
| React on-change triggers (computed fields, cascading deletes) | `Triggers` | [triggers.md](references/triggers.md) |
| Auto-generate CRUD for a table | `crud()` | [crud-http-cors.md](references/crud-http-cors.md) |
| CORS headers on HTTP actions | `corsRouter` | [crud-http-cors.md](references/crud-http-cors.md) |
| Hono web framework for HTTP routes | `HttpRouterWithHono` | [crud-http-cors.md](references/crud-http-cors.md) |
| Session tracking without auth | `SessionProvider`, `useSessionQuery` | [react-hooks.md](references/react-hooks.md) |
| Richer useQuery with status/error | `makeUseQueryWithStatus` | [react-hooks.md](references/react-hooks.md) |
| Query subscription caching | `ConvexQueryCacheProvider` | [react-hooks.md](references/react-hooks.md) |
| Parallel async operations | `asyncMap` | [core-utilities.md](references/core-utilities.md) |

## Core Patterns

### Authentication wrapper (most common pattern)

```ts
import { customQuery, customCtx } from "convex-helpers/server/customFunctions";
import { query } from "./_generated/server";

export const authenticatedQuery = customQuery(query, {
  input: async (ctx, args) => {
    const user = await getCurrentUser(ctx);
    if (!user) throw new Error("Not authenticated");
    return { ctx: { ...ctx, user }, args };
  },
});

// Usage
export const myQuery = authenticatedQuery({
  args: { id: v.id("posts") },
  handler: async (ctx, { id }) => {
    // ctx.user available
    return ctx.db.get(id);
  },
});
```

### Relationship loading

```ts
import { asyncMap } from "convex-helpers";
import { getOneFromOrThrow, getManyFrom, getManyViaOrThrow } from "convex-helpers/server/relationships";

// Requires indexes named after the field: .index("userId", ["userId"])
const author = await getOneFromOrThrow(ctx.db, "authors", "userId", user._id);
const posts = await getManyFrom(ctx.db, "posts", "authorId", author._id);
const categories = await getManyViaOrThrow(ctx.db, "postCategories", "categoryId", "postId", post._id);

// Nested loading
const postsWithComments = await asyncMap(posts, async (post) => ({
  ...post,
  comments: await getManyFrom(ctx.db, "comments", "postId", post._id),
}));
```

### Validator shorthands

```ts
import { literals, nullable, partial } from "convex-helpers/validators";

// Instead of v.union(v.literal("a"), v.literal("b"))
status: literals("active", "inactive", "archived")

// Instead of v.union(v.null(), v.string())
email: nullable(v.string())

// Make all fields optional (for patches)
const patchValidator = partial({ name: v.string(), age: v.number() });
```

## Best Practices

1. **Custom functions are the foundation** — most helpers (RLS, sessions, triggers) build on `customQuery`/`customMutation`. Define your builders early.
2. **Index your database** — relationship helpers, pagination, and streams all require indexes on queried fields.
3. **Prefer built-in when sufficient** — use built-in `.filter()` and `.paginate()` unless you need JS predicates or multiple paginations.
4. **Security first** — use `internal` functions for CRUD, add RLS before exposing publicly, validate all inputs.
5. **Use asyncMap for parallel fetches** — `await asyncMap(ids, id => ctx.db.get(id))` runs in parallel.
6. **Limit stream reads** — always set `maximumRowsRead` when using `filterWith` to prevent scanning entire tables.

## Deprecated Helpers (use components instead)

| Old helper | Replacement |
|------------|-------------|
| `convex-helpers/server/retries` | `@convex-dev/action-retrier` |
| `convex-helpers/server/migrations` | `@convex-dev/migrations` |
| `convex-helpers/server/rateLimit` | `@convex-dev/rate-limiter` |

## CLI Tools

```bash
# Generate TypeScript API spec for external repos
npx convex-helpers ts-api-spec
npx convex-helpers ts-api-spec --prod

# Generate OpenAPI spec
npx convex-helpers open-api-spec
```
