# Functions Deep Dive

## Table of Contents

- [HTTP Actions](#http-actions)
- [Scheduling Functions](#scheduling-functions)
- [Cron Jobs](#cron-jobs)
- [File Storage](#file-storage)
- [Full-Text Search](#full-text-search)
- [Pagination](#pagination)
- [Runtimes](#runtimes)
- [Error Handling Details](#error-handling-details)

---

## HTTP Actions

```typescript
// convex/http.ts
import { httpRouter } from "convex/server";
import { httpAction } from "./_generated/server";
import { api, internal } from "./_generated/api";

const http = httpRouter();

http.route({
  path: "/api/users",
  method: "GET",
  handler: httpAction(async (ctx, req) => {
    const users = await ctx.runQuery(api.users.list, {});
    return new Response(JSON.stringify(users), {
      status: 200,
      headers: { "Content-Type": "application/json" },
    });
  }),
});

http.route({
  path: "/api/users",
  method: "POST",
  handler: httpAction(async (ctx, req) => {
    const body = await req.json();
    const userId = await ctx.runMutation(api.users.create, {
      name: body.name, email: body.email,
    });
    return new Response(JSON.stringify({ userId }), {
      status: 201, headers: { "Content-Type": "application/json" },
    });
  }),
});

// Webhook handler with signature verification
http.route({
  path: "/webhooks/stripe",
  method: "POST",
  handler: httpAction(async (ctx, req) => {
    const signature = req.headers.get("stripe-signature");
    const body = await req.text();
    // Verify signature, then process
    const event = JSON.parse(body);
    await ctx.runMutation(internal.payments.handleWebhook, {
      eventType: event.type, data: event.data,
    });
    return new Response("OK", { status: 200 });
  }),
});

export default http;
```

**Request access:**
```typescript
const bytes = await req.bytes();
const text = await req.text();
const json = await req.json();
const header = req.headers.get("Content-Type");
const url = new URL(req.url);
const param = url.searchParams.get("param");
```

---

## Scheduling Functions

```typescript
// Schedule after delay (milliseconds)
await ctx.scheduler.runAfter(
  60 * 60 * 1000, // 1 hour
  internal.notifications.sendReminder,
  { userId, message: "Reminder!" }
);

// Schedule at specific timestamp
await ctx.scheduler.runAt(
  timestamp,
  internal.notifications.sendReminder,
  { userId, message: "Reminder!" }
);
```

Always schedule **internal** functions — never expose scheduled handlers to clients.

---

## Cron Jobs

```typescript
// convex/crons.ts
import { cronJobs } from "convex/server";
import { internal } from "./_generated/api";

const crons = cronJobs();

// Every 2 hours
crons.interval("cleanup", { hours: 2 }, internal.tasks.cleanupOldData, {});

// Cron schedule (every day at 9 AM UTC)
crons.cron("daily-report", "0 9 * * *", internal.tasks.generateReport, {});

export default crons;
```

**Rules:**
- Use `crons.interval` or `crons.cron` only.
- Do NOT use `crons.hourly`, `crons.daily`, `crons.weekly` — they don't exist.
- Always reference internal functions via `internal.*`.

---

## File Storage

### Upload flow

```typescript
// 1. Generate upload URL (mutation)
export const generateUploadUrl = mutation({
  args: {},
  returns: v.string(),
  handler: async (ctx) => await ctx.storage.generateUploadUrl(),
});

// 2. Client uploads file to URL via fetch POST

// 3. Save storage ID (mutation)
export const saveFile = mutation({
  args: { storageId: v.id("_storage"), fileName: v.string() },
  returns: v.null(),
  handler: async (ctx, args) => {
    await ctx.db.insert("files", {
      storageId: args.storageId, fileName: args.fileName, uploadedAt: Date.now(),
    });
    return null;
  },
});
```

### Get URL and metadata

```typescript
// Get serving URL
const url = await ctx.storage.getUrl(storageId); // null if not found

// Get metadata (use system table, NOT deprecated getMetadata)
const metadata = await ctx.db.system.get(storageId);
// { _id, _creationTime, contentType?, sha256, size }
```

### Store from action

```typescript
export const downloadAndStore = action({
  args: { url: v.string() },
  returns: v.id("_storage"),
  handler: async (ctx, args) => {
    const response = await fetch(args.url);
    const blob = await response.blob();
    return await ctx.storage.store(blob);
  },
});
```

### Delete

```typescript
await ctx.storage.delete(storageId);
```

---

## Full-Text Search

```typescript
// Schema
messages: defineTable({ body: v.string(), channel: v.string() })
  .searchIndex("search_body", { searchField: "body", filterFields: ["channel"] }),

// Query
const results = await ctx.db.query("messages")
  .withSearchIndex("search_body", (q) =>
    q.search("body", searchTerm).eq("channel", channelName)
  )
  .take(10);
```

---

## Pagination

```typescript
import { paginationOptsValidator } from "convex/server";

export const listMessages = query({
  args: { channelId: v.id("channels"), paginationOpts: paginationOptsValidator },
  handler: async (ctx, args) => {
    return await ctx.db.query("messages")
      .withIndex("by_channel", (q) => q.eq("channelId", args.channelId))
      .order("desc")
      .paginate(args.paginationOpts);
  },
});
// Returns: { page: Doc[], isDone: boolean, continueCursor: string }
// Client: { numItems: 20, cursor: null } → next page: { numItems: 20, cursor: continueCursor }
```

---

## Runtimes

| Runtime | Cold starts | Use for | Directive |
|---|---|---|---|
| **Default (V8)** | None | Queries, mutations, most actions | (none) |
| **Node.js** | Yes | Actions needing Node APIs (fs, crypto, heavy npm) | `"use node";` at file top |

Queries and mutations MUST use default runtime. Only actions can use Node.js.

```typescript
"use node";
import { action } from "./_generated/server";
import * as crypto from "crypto";
```

**Node.js version:** Set in `convex.json` → `{ "node": { "version": "20" } }` (supports 20, 22).

**External packages** (for large npm packages in Node.js actions):
```json
// convex.json
{ "node": { "externalPackages": ["*"] } }
```

---

## Error Handling Details

### ConvexError payloads

```typescript
throw new ConvexError("Simple message");                    // error.data === "Simple message"
throw new ConvexError({ code: "NOT_FOUND", message: "..." }); // error.data.code, error.data.message
throw new ConvexError({ code: 403 });                       // error.data.code === 403
```

### Client-side handling

```typescript
import { ConvexError } from "convex/values";

try { await doSomething(); }
catch (error) {
  if (error instanceof ConvexError) {
    // Application error — show to user
    const { code, message } = error.data as { code: string; message: string };
  } else {
    // System error — log and show generic message
  }
}
```

### Action retry pattern

Actions are NOT auto-retried. Handle manually:

```typescript
export const resilientFetch = action({
  args: { url: v.string() },
  returns: v.any(),
  handler: async (ctx, args) => {
    let retries = 3;
    while (retries > 0) {
      try {
        const response = await fetch(args.url);
        if (response.ok) return await response.json();
        throw new Error(`HTTP ${response.status}`);
      } catch (error) {
        retries--;
        if (retries === 0) throw error;
        await new Promise((r) => setTimeout(r, 1000));
      }
    }
  },
});
```

### Read/write limits

Convex limits documents scanned per function. Solutions:
1. Add indexes to reduce documents scanned.
2. Paginate results.
3. Split into multiple function calls.
