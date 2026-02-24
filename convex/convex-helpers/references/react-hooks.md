# React Hooks & Client Utilities

## Richer useQuery

Import: `"convex-helpers/react"`

Returns status, data, error, and boolean flags instead of just `undefined | T`.

```ts
import { makeUseQueryWithStatus } from "convex-helpers/react";
import { useQueries } from "convex/react";

// Define once in your app
export const useQueryWithStatus = makeUseQueryWithStatus(useQueries);

// Usage
function MyComponent() {
  const { status, data, error, isSuccess, isPending, isError } =
    useQueryWithStatus(api.foo.bar, { myArg: 123 });

  if (isPending) return <Loading />;
  if (isError) return <Error message={error.message} />;
  if (isSuccess) return <DataView data={data} />;
}
```

Return type:
```ts
| { status: "success"; data: T; isSuccess: true; isPending: false; isError: false }
| { status: "pending"; data: undefined; isPending: true; isSuccess: false; isError: false }
| { status: "error"; error: Error; isError: true; isSuccess: false; isPending: false }
```

---

## Session Tracking

Server import: `"convex-helpers/server/sessions"` | React import: `"convex-helpers/react/sessions"`

Track users via localStorage session ID — no auth required.

### Server setup

```ts
import { customQuery } from "convex-helpers/server/customFunctions";
import { SessionIdArg } from "convex-helpers/server/sessions";

export const queryWithSession = customQuery(query, {
  args: SessionIdArg,  // { sessionId: vSessionId }
  input: async (ctx, { sessionId }) => {
    const anonymousUser = await getAnonymousUser(ctx, sessionId);
    return { ctx: { ...ctx, anonymousUser }, args: {} };
  },
});

export const mySessionQuery = queryWithSession({
  args: { arg1: v.number() },
  handler: async (ctx, args) => ctx.anonymousUser,
});
```

### React client

```tsx
import { SessionProvider } from "convex-helpers/react/sessions";

function App() {
  return (
    <ConvexProvider client={convex}>
      <SessionProvider>
        <YourApp />
      </SessionProvider>
    </ConvexProvider>
  );
}
```

### Using session hooks

```tsx
import { useSessionQuery, useSessionMutation } from "convex-helpers/react/sessions";

function MyComponent() {
  const results = useSessionQuery(api.myModule.mySessionQuery, { arg1: 1 });
  const mutate = useSessionMutation(api.myModule.mySessionMutation);
  const handleClick = async () => await mutate({ someArg: "value" });
}
```

### In actions

```ts
import { runSessionFunctions } from "convex-helpers/server/sessions";

export const myAction = action({
  args: { sessionId: vSessionId },
  handler: async (ctx, { sessionId }) => {
    const { runSessionQuery, runSessionMutation } = runSessionFunctions(ctx, sessionId);
    const result = await runSessionQuery(internal.users.getBySession, {});
    await runSessionMutation(internal.users.trackVisit, { userId: result._id });
  },
});
```

---

## Query Caching

Import: `"convex-helpers/react/cache"` | Next.js: `"convex-helpers/react/cache/provider"` + `"convex-helpers/react/cache/hooks"`

Keeps subscriptions alive after unmount for fast re-mount. **Uses more bandwidth** — optimizes UX, not DB bandwidth.

### Setup

```tsx
import { ConvexQueryCacheProvider } from "convex-helpers/react/cache";

export default function RootLayout({ children }) {
  return (
    <ConvexClientProvider>
      <ConvexQueryCacheProvider>
        {children}
      </ConvexQueryCacheProvider>
    </ConvexClientProvider>
  );
}
```

### Provider props

```tsx
<ConvexQueryCacheProvider
  expiration={300000}    // ms to keep unmounted subs (default: 5 min)
  maxIdleEntries={250}   // max cached subscriptions (default: 250)
  debug={false}          // console logs every 3s (default: false)
>
```

### Cached hooks (drop-in replacements)

```tsx
import { useQuery, useQueries, usePaginatedQuery } from "convex-helpers/react/cache";

const users = useQuery(api.users.getAll);

const { results, status, loadMore } = usePaginatedQuery(
  api.messages.list, {}, { initialNumItems: 10 }
);

// For paginator/getPage (non-reactive), pass customPagination:
usePaginatedQuery(api.messages.list, {}, { initialNumItems: 10, customPagination: true });
```
