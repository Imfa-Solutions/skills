# Filter, Pagination & Streams

## Filter

Import: `"convex-helpers/server/filter"`

Apply arbitrary JS/TS filters to database queries. **Performance warning:** similar to `.filter()` — always narrow with indexes first.

```ts
import { filter } from "convex-helpers/server/filter";

// Full table scan with JS filter
const evens = await filter(
  ctx.db.query("counter_table"),
  (c) => c.counter % 2 === 0
).collect();

// Index + JS filter (recommended)
const shortMessages = await filter(
  ctx.db.query("messages").withIndex("by_author", q => q.eq("author", author)),
  (msg) => msg.body.length < 10
).collect();

// Async predicates
const verified = await filter(
  ctx.db.query("messages"),
  async (msg) => {
    const author = await ctx.db.get(msg.author);
    return author?.verified === true;
  }
).collect();
```

Supported chain methods: `.collect()`, `.first()`, `.unique()`, `.take(n)`, `.paginate(opts)`, `.order()`, `.withIndex()`, `.withSearchIndex()`, `.filter()`

---

## Manual Pagination

Import: `"convex-helpers/server/pagination"`

### getPage

More control than built-in `.paginate()`.

```ts
import { getPage } from "convex-helpers/server/pagination";
import schema from "./schema";

// First page
const { page, indexKeys, hasMore } = await getPage(ctx, { table: "messages" });

// Next page
const { page: page2 } = await getPage(ctx, {
  table: "messages",
  startIndexKey: indexKeys[indexKeys.length - 1],
});

// Custom index + page size
const { page } = await getPage(ctx, {
  table: "users",
  index: "by_name",
  schema,
  targetMaxRows: 1000,
});

// Between two positions
const { page } = await getPage(ctx, {
  table: "messages",
  startIndexKey,
  endIndexKey,
});

// Starting at a timestamp
const { page } = await getPage(ctx, {
  table: "messages",
  startIndexKey: [Date.now() - 24 * 60 * 60 * 1000],
  startInclusive: true,
  order: "desc",
});
```

### paginator

Alternative to `.paginate()` that can be called multiple times per query.

```ts
import { paginator } from "convex-helpers/server/pagination";
import schema from "./schema";

export const list = query({
  args: { opts: paginationOptsValidator },
  handler: async (ctx, { opts }) => {
    return await paginator(ctx.db, schema)
      .query("messages")
      .withIndex("by_author", q => q.eq("author", args.author))
      .order("desc")
      .paginate(opts);
  },
});
```

For reactive queries, use `endCursor` to prevent holes/overlaps between pages.

---

## Query Streams

Import: `"convex-helpers/server/stream"`

Compose complex queries — equivalent to SQL's UNION, WHERE, JOIN.

### Basic stream

```ts
import { stream } from "convex-helpers/server/stream";
import schema from "./schema";

const messages = stream(ctx.db, schema)
  .query("messages")
  .withIndex("by_author", q => q.eq("author", userId));
```

### mergedStream (UNION ALL)

Combine multiple streams, maintaining order.

```ts
import { stream, mergedStream } from "convex-helpers/server/stream";

const authorStreams = authorIds.map(authorId =>
  stream(ctx.db, schema)
    .query("messages")
    .withIndex("by_author", q => q.eq("author", authorId))
);

const allMessages = mergedStream(authorStreams, ["author", "_creationTime"]);
const page = await allMessages.paginate(paginationOpts);
```

### filterWith (WHERE)

Async predicates applied before pagination.

```ts
const filteredStream = stream(ctx.db, schema)
  .query("messages")
  .filterWith(async (msg) => {
    const author = await ctx.db.get(msg.author);
    return author?.verified === true;
  });

const page = await filteredStream.paginate({
  ...paginationOpts,
  maximumRowsRead: 100,  // Always set a safety limit
});
```

### map

Transform each document.

```ts
const withAuthor = stream(ctx.db, schema)
  .query("messages")
  .map(async (msg) => ({
    ...msg,
    author: await ctx.db.get(msg.author),
  }));
```

### flatMap (JOIN)

Expand each document into a stream.

```ts
const channelMemberships = stream(ctx.db, schema)
  .query("channelMemberships")
  .withIndex("userId", q => q.eq("userId", userId));

const messages = channelMemberships
  .map(async (m) => ctx.db.get(m.channelId))
  .flatMap(
    async (channel) =>
      stream(ctx.db, schema)
        .query("messages")
        .withIndex("channelId", q => q.eq("channelId", channel._id))
        .map(async (msg) => ({ ...channel, ...msg })),
    ["channelId", "_creationTime"]
  );

const page = await messages.paginate(paginationOpts);
```

### Complete stream example

```ts
export const list = query({
  args: { paginationOpts: paginationOptsValidator },
  handler: async (ctx, { paginationOpts }) => {
    return await stream(ctx.db, schema)
      .query("messages")
      .order("desc")
      .filterWith(async (message) => {
        const author = await ctx.db.get(message.author);
        return author !== null && author.verified;
      })
      .paginate({ ...paginationOpts, maximumRowsRead: 100 });
  },
});
```
