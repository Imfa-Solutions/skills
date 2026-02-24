# Relationship Helpers

Import: `"convex-helpers/server/relationships"`

Traverse database relationships without boilerplate. **Requires indexes on queried fields.**

## getOrThrow

Get a document by ID, throw if not found.

```ts
import { getOrThrow } from "convex-helpers/server/relationships";

const user = await getOrThrow(ctx, "users", userId);
const user = await getOrThrow(ctx, userId);  // table inferred from ID type
```

## getAll / getAllOrThrow

Get multiple documents by ID.

```ts
import { getAll, getAllOrThrow } from "convex-helpers/server/relationships";

const users = await getAll(ctx.db, "users", userIds);        // (Doc | null)[]
const users = await getAllOrThrow(ctx.db, userIds);           // Doc[] — throws if any missing
const users = await getAll(ctx.db, new Set([id1, id2, id3])); // Works with Set
```

## getOneFrom / getOneFromOrThrow

One-to-one relationship — get by indexed field value.

**Requires:** Index named after the field, e.g., `.index("userId", ["userId"])`

```ts
import { getOneFrom, getOneFromOrThrow } from "convex-helpers/server/relationships";

// Schema: authors: defineTable({ userId: v.id("users") }).index("userId", ["userId"])
const author = await getOneFrom(ctx.db, "authors", "userId", user._id);       // Doc | null
const author = await getOneFromOrThrow(ctx.db, "authors", "userId", user._id); // Doc — throws
```

## getManyFrom

One-to-many relationship — get all docs matching a field value.

```ts
import { getManyFrom } from "convex-helpers/server/relationships";

// Schema: posts: defineTable({ authorId: v.id("authors") }).index("authorId", ["authorId"])
const posts = await getManyFrom(ctx.db, "posts", "authorId", author._id);  // Doc[]
```

## getManyVia / getManyViaOrThrow

Many-to-many relationship — get docs through a join table.

```ts
import { getManyVia, getManyViaOrThrow } from "convex-helpers/server/relationships";

// Schema:
// postCategories: defineTable({
//   postId: v.id("posts"),
//   categoryId: v.id("categories")
// }).index("by_post", ["postId", "categoryId"])

const categories = await getManyVia(
  ctx.db,
  "postCategories",  // join table
  "categoryId",      // field to retrieve
  "postId",          // field to match
  post._id           // value to match
);

const categories = await getManyViaOrThrow(
  ctx.db, "postCategories", "categoryId", "postId", post._id
);
```

## Complete example — nested loading

```ts
import { getOneFromOrThrow, getManyFrom, getManyViaOrThrow } from "convex-helpers/server/relationships";
import { asyncMap } from "convex-helpers";

const author = await getOneFromOrThrow(ctx.db, "authors", "userId", user._id);

const posts = await asyncMap(
  await getManyFrom(ctx.db, "posts", "authorId", author._id),
  async (post) => ({
    ...post,
    comments: await getManyFrom(ctx.db, "comments", "postId", post._id),
    categories: await getManyViaOrThrow(ctx.db, "postCategories", "categoryId", "postId", post._id),
  })
);
```
