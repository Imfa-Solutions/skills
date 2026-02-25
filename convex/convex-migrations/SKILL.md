---
name: convex-migrations
description: >
  Convex Migrations component (@convex-dev/migrations) — online, batched data migrations for
  Convex databases. Define migrations that transform documents in-place, run them asynchronously
  in configurable batches without downtime, track progress, resume from failures, and manage
  schema evolution safely. Covers installation, setup (convex.config.ts, Migrations client),
  defining migrations (migrateOne, shorthand patch syntax, customRange with indexes, multi-table),
  running migrations (single, serial, generic runner, programmatic), advanced config (batch size,
  parallelize, custom internalMutation, dry run, runToCompletion for tests), schema evolution
  strategy, common patterns (add required field, rename field, convert types, denormalization),
  testing with convex-test, and troubleshooting (OCC conflicts, status tracking, cancellation).
  Use when migrating data in Convex, changing schema shape, backfilling fields, renaming columns,
  converting field types, running batch data transforms, or managing database schema evolution.
  Triggers on: @convex-dev/migrations, migrations.define, migrations.runner, migrateOne,
  customRange, runToCompletion, runSerially, migrations.cancel, migrations.getStatus,
  "migrate data", "backfill field", "rename field migration", "schema migration",
  "add required field", "convert field type", "batch update documents",
  "how do I migrate data in Convex", "change schema safely", "online migration".
---

# Convex Migrations — Online Data Migration Component

> Batch-process existing documents to match new schema requirements — no downtime, resumable, state-tracked.

## When to Use This

Use `@convex-dev/migrations` whenever you need to transform existing data in a Convex database — adding fields, renaming fields, converting types, backfilling computed values, or any bulk document update. Migrations run asynchronously in batches so the app stays available throughout.

## Installation & Setup

```bash
npm install @convex-dev/migrations
```

### Register the component

```typescript
// convex/convex.config.ts
import { defineApp } from "convex/server";
import migrations from "@convex-dev/migrations/convex.config.js";

const app = defineApp();
app.use(migrations);

export default app;
```

### Initialize the client

```typescript
// convex/migrations.ts
import { Migrations } from "@convex-dev/migrations";
import { components } from "./_generated/api.js";
import { DataModel } from "./_generated/dataModel.js";

export const migrations = new Migrations<DataModel>(components.migrations);
export const run = migrations.runner();
```

The `DataModel` generic gives you type safety on table names and document shapes inside `migrateOne`.

## Schema Evolution Workflow

This is the safe, standard sequence for changing a schema in a live Convex app:

1. **Add new field as optional** in schema (`v.optional(...)`)
2. **Update app code** to handle both old and new data shapes
3. **Deploy** schema + code changes
4. **Define & run** the migration to populate/transform the new field
5. **Make field required** in schema once migration completes
6. **Remove** backward-compatibility code

Following this order prevents schema validation errors during the transition window.

## Defining Migrations

### Basic — full control with ctx

```typescript
export const setDefaultValue = migrations.define({
  table: "users",
  migrateOne: async (ctx, user) => {
    if (user.optionalField === undefined) {
      await ctx.db.patch(user._id, { optionalField: "default" });
    }
  },
});
```

### Shorthand — return a patch object

When your migration simply patches the document, return the patch directly instead of calling `ctx.db.patch()`:

```typescript
export const clearField = migrations.define({
  table: "myTable",
  migrateOne: () => ({ optionalField: undefined }),
});
// Equivalent to: ctx.db.patch(doc._id, { optionalField: undefined })
```

### Subset migration with customRange

Use `customRange` to migrate only documents matching an index condition — much faster than scanning the whole table:

```typescript
export const fixEmptyRequired = migrations.define({
  table: "myTable",
  customRange: (query) =>
    query.withIndex("by_requiredField", (q) => q.eq("requiredField", "")),
  migrateOne: async (_ctx, doc) => {
    return { requiredField: "<unknown>" };
  },
});
```

### Multi-table — read/write across tables

`migrateOne` has full `ctx` access, so you can query and write to other tables:

```typescript
export const backfillUserStats = migrations.define({
  table: "users",
  migrateOne: async (ctx, user) => {
    const postCount = await ctx.db
      .query("posts")
      .withIndex("by_author", (q) => q.eq("authorId", user._id))
      .collect()
      .then((posts) => posts.length);
    return { postCount };
  },
});
```

## Running Migrations

### Single migration runner

```typescript
export const runSetDefault = migrations.runner(internal.migrations.setDefaultValue);
```

```bash
npx convex run migrations:runSetDefault
npx convex run migrations:runSetDefault --prod   # production
```

### Generic runner (specify by name)

```typescript
export const run = migrations.runner();
```

```bash
npx convex run migrations:run '{"fn": "migrations:setDefaultValue"}'
```

### Serial runner (ordered dependencies)

```typescript
export const runAll = migrations.runner([
  internal.migrations.setDefaultValue,
  internal.migrations.fixEmptyRequired,
  internal.migrations.backfillUserStats,
]);
```

```bash
npx convex run migrations:runAll
```

### Programmatic execution

```typescript
// Single
await migrations.runOne(ctx, internal.migrations.setDefaultValue);

// Serial
await migrations.runSerially(ctx, [
  internal.migrations.setDefaultValue,
  internal.migrations.fixEmptyRequired,
]);
```

### Runner behavior

| Feature | Detail |
|---|---|
| Duplicate prevention | Refuses to start if already running |
| Resume from failure | Continues from last successful batch cursor |
| Explicit cursor | Pass `cursor: null` to restart from beginning |
| Dry run | Pass `dryRun: true` to validate without committing |

## Advanced Configuration

### Batch size

Default is 100. Reduce for large documents or high OCC contention:

```typescript
// Per-migration
export const clearField = migrations.define({
  table: "myTable",
  batchSize: 10,
  migrateOne: () => ({ optionalField: undefined }),
});

// Per-run override
await migrations.runOne(ctx, internal.migrations.clearField, { batchSize: 1 });
```

### Parallelize batches

Run `migrateOne` calls concurrently within each batch. Only use when callbacks don't read-then-write overlapping data:

```typescript
export const clearField = migrations.define({
  table: "myTable",
  parallelize: true,
  migrateOne: () => ({ optionalField: undefined }),
});
```

### Custom internalMutation

Swap the internal mutation for one with custom DB behavior (validation, triggers via convex-helpers):

```typescript
import { internalMutation } from "./functions"; // your custom builder
import { Migrations } from "@convex-dev/migrations";
import { components } from "./_generated/api";

export const migrations = new Migrations(components.migrations, {
  internalMutation,
});
```

### Shorthand prefix

Avoid typing full paths when using the generic runner:

```typescript
export const migrations = new Migrations(components.migrations, {
  migrationsLocationPrefix: "migrations:",
});
```

```bash
npx convex run migrations:run '{"fn": "myNewMutation"}'
```

### Synchronous execution (tests & actions)

`runToCompletion` blocks until the migration finishes — use in tests or actions, never in mutations:

```typescript
import { runToCompletion } from "@convex-dev/migrations";

export const myAction = internalAction({
  args: {},
  handler: async (ctx) => {
    await runToCompletion(
      ctx,
      components.migrations,
      internal.example.setDefaultValue
    );
  },
});
```

## Monitoring & Control

### Check status

```bash
npx convex run --component migrations lib:getStatus --watch
```

```typescript
const status = await migrations.getStatus(ctx, {
  migrations: [internal.migrations.setDefaultValue],
});
```

### Cancel a migration

```bash
npx convex run --component migrations lib:cancel '{"name": "migrations:myMigration"}'
```

```typescript
await migrations.cancel(ctx, internal.migrations.myMigration);
```

### Restart from beginning

```bash
npx convex run migrations:runIt '{"cursor": null}'
```

## Troubleshooting

| Issue | Cause | Solution |
|---|---|---|
| OCC conflicts | High write contention | Reduce `batchSize` |
| Transaction too large | Large documents in batch | Reduce `batchSize` |
| Migration stuck | Worker crashed | Check logs, cancel and retry |
| Schema validation fails | Migration output doesn't match schema | Fix `migrateOne`, restart with `cursor: null` |
| Partial progress | Error mid-migration | Resume (automatic) or restart from cursor |

## Best Practices

- **Idempotent migrations** — always check if the change is already applied before patching. This makes migrations safe to re-run.
- **Test with dryRun** — validate migrations before committing changes, especially on production.
- **Reduce batch size** proactively for tables with large documents or high write traffic.
- **Use `customRange`** with an index when only a subset of documents need migration — avoids scanning the entire table.
- **Off-peak execution** — run large migrations during low-traffic periods.

## Code Organization

```
convex/
├── migrations.ts          # Migrations client + runners
├── migrations/
│   ├── userMigrations.ts  # User-related migrations
│   └── postMigrations.ts  # Post-related migrations
└── convex.config.ts       # Component registration
```

## Reference Files

- **Common patterns & testing**: Adding required fields, renaming fields, converting types, denormalization, unit testing with convex-test → See [references/patterns-and-testing.md](references/patterns-and-testing.md)
