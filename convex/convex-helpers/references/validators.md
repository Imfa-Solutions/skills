# Validator Utilities

Import: `"convex-helpers/validators"`

Shorthand validators and utilities for Convex schema definitions.

## literals

Union of literal values — concise alternative to chaining `v.union(v.literal(...))`.

```ts
import { literals } from "convex-helpers/validators";

// Instead of: v.union(v.literal("active"), v.literal("inactive"))
status: literals("active", "inactive")

// With array
const myLiterals = ["a", "b", "c"] as const;
const validator = literals(...myLiterals);
```

## nullable

Make a validator nullable.

```ts
import { nullable } from "convex-helpers/validators";

// Instead of: v.union(v.null(), v.string())
email: nullable(v.string())
```

## partial

Make all fields in an object optional — useful for patch/update args.

```ts
import { partial } from "convex-helpers/validators";

// For PropertyValidators
const patchValidator = partial({ name: v.string(), age: v.number() });
// { name: VOptional<VString>, age: VOptional<VFloat64> }

// For VObject
const partialDoc = partial(v.object({ name: v.string(), age: v.number() }));

// For VUnion
const partialUnion = partial(v.union(
  v.object({ type: v.literal("a"), a: v.string() }),
  v.object({ type: v.literal("b"), b: v.number() })
));
```

## deprecated

Mark a field as deprecated — accepts any value, returns undefined.

```ts
import { deprecated } from "convex-helpers/validators";

// In schema migration
oldField: deprecated,
```

## brandedString

Create a branded string type for compile-time type safety.

```ts
import { brandedString } from "convex-helpers/validators";
import { Infer } from "convex/values";

const emailValidator = brandedString("email");
export type Email = Infer<typeof emailValidator>;

// Schema usage
email: emailValidator,

// Type-safe functions
function sendEmail(to: Email) { /* ... */ }
```

## systemFields / withSystemFields

Get or add system fields (`_id`, `_creationTime`) validators.

```ts
import { systemFields, withSystemFields } from "convex-helpers/validators";

const sysFields = systemFields("users");
// { _id: VId<"users">, _creationTime: VFloat64 }

const fullValidator = withSystemFields("users", { name: v.string(), email: v.string() });
// { name, email, _id, _creationTime }
```

## doc

Validate a complete document including system fields — useful for `returns` validators.

```ts
import { doc } from "convex-helpers/validators";
import schema from "./schema";

export const getUser = query({
  args: { id: v.id("users") },
  returns: doc(schema, "users"),
  handler: async (ctx, { id }) => ctx.db.get(id),
});
```

## typedV

Enhanced `v` with schema-aware `id()` and `doc()` methods.

```ts
import { typedV } from "convex-helpers/validators";
import schema from "./schema";

const vv = typedV(schema);

args: {
  id: vv.id("users"),     // VId<"users">
  data: vv.doc("users"),  // Full document validator with system fields
}
```

## validate

Validate a value against a validator at runtime.

```ts
import { validate } from "convex-helpers/validators";

const result = validate(myValidator, value);
// { success: true, data: T } | { success: false, error: ValidationError }

validate(myValidator, value, { throw: true });  // Throws on invalid

validate(vv.id("users"), accountId, { db: ctx.db });  // With DB ID validation
```
