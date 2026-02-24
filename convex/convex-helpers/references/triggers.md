# Triggers

Import: `"convex-helpers/server/triggers"`

Register functions that run whenever table data changes. Runs atomically in the same transaction as the mutation.

## Setup

```ts
import { mutation as rawMutation } from "./_generated/server";
import { DataModel } from "./_generated/dataModel";
import { Triggers } from "convex-helpers/server/triggers";
import { customCtx, customMutation } from "convex-helpers/server/customFunctions";

const triggers = new Triggers<DataModel>();

// Register triggers...
triggers.register("users", async (ctx, change) => { /* ... */ });

// Create wrapped mutation — triggers ONLY run through this
export const mutation = customMutation(rawMutation, customCtx(triggers.wrapDB));
```

## Change object

```ts
type Change = {
  id: Id<TableName>;
} & (
  | { operation: "insert"; oldDoc: null; newDoc: Document }
  | { operation: "update"; oldDoc: Document; newDoc: Document }
  | { operation: "delete"; oldDoc: Document; newDoc: null }
);
```

## Trigger patterns

### Computed field

```ts
triggers.register("users", async (ctx, change) => {
  if (change.newDoc) {
    const fullName = `${change.newDoc.firstName} ${change.newDoc.lastName}`;
    if (change.newDoc.fullName !== fullName) {
      await ctx.db.patch(change.id, { fullName });
    }
  }
});
```

### Denormalized count

```ts
triggers.register("users", async (ctx, change) => {
  const countDoc = (await ctx.db.query("userCount").unique())!;
  if (change.operation === "insert") {
    await ctx.db.patch(countDoc._id, { count: countDoc.count + 1 });
  } else if (change.operation === "delete") {
    await ctx.db.patch(countDoc._id, { count: countDoc.count - 1 });
  }
});
```

### Cascading deletes

```ts
triggers.register("users", async (ctx, change) => {
  if (change.operation === "delete") {
    const messages = await getManyFrom(ctx.db, "messages", "owner", change.id);
    await asyncMap(messages, (msg) => ctx.db.delete(msg._id));
  }
});
```

### Debounced async processing

```ts
const scheduled: Record<Id, Id<"_scheduled_functions">> = {};

triggers.register("users", async (ctx, change) => {
  if (scheduled[change.id]) await ctx.scheduler.cancel(scheduled[change.id]);
  scheduled[change.id] = await ctx.scheduler.runAfter(
    0, internal.users.updateExternal, { user: change.newDoc }
  );
});
```

### Validation

```ts
triggers.register("users", async (ctx, change) => {
  if (change.newDoc?.age < 0) throw new Error("Age cannot be negative");
});
```

## Semantics

- Triggers run atomically with the write (same transaction)
- Parallel writes (`Promise.all`) are serialized
- Recursive triggers execute breadth-first
- All triggers run even if one throws; first error is rethrown
- Use `ctx.innerDb` to write without triggering more triggers
- Triggers ONLY run through wrapped mutations

## ESLint rule to enforce wrapped mutations

```json
{
  "rules": {
    "no-restricted-imports": ["error", {
      "paths": [{
        "name": "./_generated/server",
        "importNames": ["mutation", "internalMutation"],
        "message": "Use the wrapped mutation from convex/utils.ts instead"
      }]
    }]
  }
}
```
