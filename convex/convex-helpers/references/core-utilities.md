# Core Utilities

Import: `"convex-helpers"`

## asyncMap

Run an async function over a list in parallel. List can be a Promise or iterable (Set, Array).

```ts
import { asyncMap } from "convex-helpers";

const users = await asyncMap(userIds, async (id) => ctx.db.get(id));
const docs = await asyncMap(new Set(["id1", "id2"]), async (id) => ctx.db.get(id));
const results = await asyncMap(Promise.resolve(ids), async (id) => fetchUser(id));
```

Signature: `asyncMap<F, T>(list: Iterable<F> | Promise<Iterable<F>>, fn: (item: F, index: number) => Promise<T>): Promise<T[]>`

- All operations run in parallel — order of execution is not guaranteed
- For sequential operations, use a `for...of` loop instead

## pruneNull

Filter out null elements, narrowing type from `(T | null)[]` to `T[]`.

```ts
import { pruneNull } from "convex-helpers";

const maybeUsers = await asyncMap(userIds, (id) => ctx.db.get(id));
const users = pruneNull(maybeUsers); // T[] instead of (T | null)[]
```

## nullThrows

Throw if value is null, otherwise return with refined type.

```ts
import { nullThrows, NullDocumentError } from "convex-helpers";

const user = nullThrows(await ctx.db.get(userId), `User ${userId} not found`);

try {
  const doc = nullThrows(await ctx.db.get(id));
} catch (e) {
  if (e instanceof NullDocumentError) { /* handle */ }
}
```

## pick / omit

TypeScript-style utilities for validator objects.

```ts
import { pick, omit } from "convex-helpers";

const publicFields = pick(userValidator.fields, ["name", "email"]);
const safeFields = omit(userValidator.fields, ["password"]);
```
