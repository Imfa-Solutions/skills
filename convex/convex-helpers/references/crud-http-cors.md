# CRUD, Hono HTTP & CORS

## CRUD Utilities

Import: `"convex-helpers/server/crud"`

Generate basic CRUD operations for a table. **Recommended for prototyping or internal functions only** (add RLS before exposing publicly).

### Basic usage

```ts
import { crud } from "convex-helpers/server/crud";
import schema from "./schema.js";

// Generates: create, read, update, destroy, paginate
export const { create, read, update, destroy, paginate } = crud(schema, "users");
```

### Using CRUD functions

```ts
const user = await ctx.runQuery(internal.users.read, { id: userId });

await ctx.runMutation(internal.users.update, {
  id: userId,
  patch: { status: "inactive" },
});

const deleted = await ctx.runMutation(internal.users.destroy, { id: userId });

const page = await ctx.runQuery(internal.users.paginate, {
  paginationOpts: { numItems: 10, cursor: null },
});
```

### Custom visibility (public)

```ts
import { query, mutation } from "./_generated/server";

export const { create, read, update, destroy } = crud(schema, "users", query, mutation);
```

### With custom functions (RLS)

```ts
const { create, read, update, destroy } = crud(schema, "users", queryWithRLS, mutationWithRLS);
```

---

## Hono HTTP Endpoints

Import: `"convex-helpers/server/hono"` | Requires: `npm install hono`

Use the Hono web framework for advanced HTTP endpoint definitions.

### Setup (convex/http.ts)

```ts
import { Hono } from "hono";
import { HonoWithConvex, HttpRouterWithHono } from "convex-helpers/server/hono";
import { ActionCtx } from "./_generated/server";

const app: HonoWithConvex<ActionCtx> = new Hono();

app.get("/", async (c) => c.json("Hello world!"));

app.get("/users/:id", async (c) => {
  const id = c.req.param("id");
  const user = await c.ctx.runQuery(internal.users.get, { id });
  return c.json(user);
});

app.post("/webhook", async (c) => {
  const body = await c.req.json();
  await c.ctx.runMutation(internal.webhooks.process, body);
  return c.json({ success: true });
});

export default new HttpRouterWithHono(app);
```

### Convex context in Hono handlers

`c.ctx` provides: `runQuery()`, `runMutation()`, `runAction()`, `auth`

---

## CORS Support

Import: `"convex-helpers/server/cors"`

Add CORS headers to Convex httpAction routes.

### Setup

```ts
import { corsRouter } from "convex-helpers/server/cors";
import { httpRouter } from "convex/server";

const http = httpRouter();

const cors = corsRouter(http, {
  allowedOrigins: ["http://localhost:8080"],  // Default: ["*"]
  allowedMethods: ["GET", "POST"],
  allowedHeaders: ["Content-Type"],
  exposedHeaders: ["Custom-Header"],
  allowCredentials: true,
  browserCacheMaxAge: 60,               // Default: 86400
  enforceAllowOrigins: true,            // Default: false (returns 403 if not allowed)
  debug: true,
});

// CORS-enabled routes
cors.route({
  path: "/foo",
  method: "GET",
  handler: httpAction(async () => new Response("ok")),
});

// Per-route override
cors.route({
  path: "/api",
  method: "POST",
  handler: httpAction(async () => new Response("ok")),
  allowedOrigins: ["https://app.example.com"],
});

// Non-CORS routes still work via http.route()
http.route({
  path: "/internal",
  method: "GET",
  handler: httpAction(async () => new Response("ok")),
});

export default http;
```

### Dynamic origins

```ts
const cors = corsRouter(http, {
  allowedOrigins: async (req: Request) => {
    return ["https://app.example.com"];
  },
});
```
