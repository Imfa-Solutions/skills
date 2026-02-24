# Advanced ConvexFS Topics

## Table of Contents

- [Multiple Filesystems](#multiple-filesystems)
- [Garbage Collection Details](#garbage-collection-details)
- [Disaster Recovery](#disaster-recovery)
- [Testing](#testing)
- [Bunny.net Setup Guide](#bunnynet-setup-guide)
- [Production Checklist](#production-checklist)
- [TypeScript Types](#typescript-types)
- [Conflict Error Codes](#conflict-error-codes)
- [Troubleshooting](#troubleshooting)

---

## Multiple Filesystems

Mount separate ConvexFS instances for different use cases:

```typescript
// convex/convex.config.ts
app.use(fs, { name: "userFiles" });
app.use(fs, { name: "staticAssets" });
```

```typescript
// convex/userFiles.ts
export const userFiles = new ConvexFS(components.userFiles, {
  storage: { type: "bunny", /* ... */ },
  blobGracePeriod: 604800, // 7 days for user content
});

// convex/staticAssets.ts
export const staticAssets = new ConvexFS(components.staticAssets, {
  storage: { type: "bunny", /* ... */ },
  blobGracePeriod: 86400, // 24 hours for static assets
});
```

```typescript
// convex/http.ts — separate routes per filesystem
registerRoutes(http, components.userFiles, userFiles, {
  pathPrefix: "/user-files",
  uploadAuth: async (ctx) => { /* user auth */ },
  downloadAuth: async (ctx, blobId, path) => { /* path-based auth */ },
});

registerRoutes(http, components.staticAssets, staticAssets, {
  pathPrefix: "/static",
  uploadAuth: async (ctx) => { /* admin only */ },
  downloadAuth: async () => true, // public
});
```

---

## Garbage Collection Details

Three automatic background jobs:

| Job | Schedule | Purpose | TTL |
|---|---|---|---|
| **UGC** (Upload GC) | Hourly at :00 | Clean abandoned uploads | 4 hours |
| **BGC** (Blob GC) | Hourly at :20 | Delete orphaned blobs (refCount=0) | `blobGracePeriod` |
| **FGC** (File GC) | Every 15 seconds | Delete files past `expiresAt` | Immediate |

**Reference counting:**
- `commitFiles` → refCount + 1
- `copy` → refCount + 1 (zero-copy, no data duplication)
- `delete` → refCount - 1
- `move` → no refCount change (re-reference)
- refCount reaches 0 → blob becomes orphaned → GC deletes after grace period

**Freezing GC:**
Set `config.freezeGc = true` in the Convex dashboard to pause all garbage collection (useful for debugging or disaster recovery).

---

## Disaster Recovery

### Restore a deleted file

```typescript
// Find blobId in dashboard (blobs table, refCount=0, within grace period)
await ctx.runMutation(internal.ops.basics.restore, {
  blobId: "the-blob-id",
  path: "/restored/file.txt",
});
```

### Freeze GC immediately

Set `config.freezeGc = true` in the Convex dashboard to prevent permanent deletion while investigating.

### Clear all files (development only)

```typescript
// First enable: config.allowClearAllFiles = true
await ctx.runAction(internal.ops.basics.clearAllFiles, {});
```

---

## Testing

Use the test blob store with `convex-test`:

```typescript
// convex/fs.ts
import { ConvexFS } from "convex-fs";
import { components } from "./_generated/api";

const isTest = process.env.NODE_ENV === "test";

export const fs = new ConvexFS(components.fs, {
  storage: isTest
    ? { type: "test" }
    : {
        type: "bunny",
        apiKey: process.env.BUNNY_API_KEY!,
        storageZoneName: process.env.BUNNY_STORAGE_ZONE!,
        cdnHostname: process.env.BUNNY_CDN_HOSTNAME!,
      },
});
```

The `{ type: "test" }` storage backend stores blobs in memory — no bunny.net account needed for tests.

---

## Bunny.net Setup Guide

### Step-by-step

1. **Create account** at https://bunny.net

2. **Create Storage Zone:**
   - Storage → Add Storage Zone
   - Choose **Edge (SSD)** tier for production
   - Main region: **Frankfurt (DE)** (mandatory for Edge tier)
   - Select replication regions as needed

3. **Connect Pull Zone (CDN):**
   - Connected pull zones → + Connect Pull Zone
   - Name: `{storage-zone}-cdn`
   - Enable all pricing zones for global coverage

4. **Enable Token Authentication:**
   - Pull Zone → Security → Token Authentication
   - Enable "URL Token Authentication"
   - Authentication Type: **SHA256 (Advanced)**
   - Copy the **Token Authentication Key** → `BUNNY_TOKEN_KEY`

5. **Get API Key:**
   - Account → API → Add API Key → `BUNNY_API_KEY`
   - Or: Storage Zone → FTP & API Access → API Key

### Storage region codes

| Code | Region |
|---|---|
| *(empty)* | Frankfurt (default) |
| `uk` | London |
| `ny` | New York |
| `la` | Los Angeles |
| `sg` | Singapore |
| `se` | Stockholm |
| `br` | São Paulo |
| `jh` | Johannesburg |
| `syd` | Sydney |

---

## Production Checklist

- [ ] Separate bunny.net zones for dev/CI and production
- [ ] Set project environment variable defaults for dev/CI
- [ ] Override production credentials in production deployment
- [ ] Configure `blobGracePeriod` (7–14 days recommended for critical apps)
- [ ] Enable token authentication on CDN
- [ ] Implement proper auth checks for upload AND download
- [ ] Use path-based authorization in `downloadAuth`
- [ ] Add logging for file operations (aids recovery)
- [ ] Set up monitoring for GC jobs
- [ ] Set appropriate `downloadUrlTtl` for your use case

---

## TypeScript Types

```typescript
interface FileMetadata {
  path: string;
  blobId: string;
  contentType: string;
  size: number;
  attributes?: { expiresAt?: number };
}

interface ConflictErrorData {
  code: ConflictCode;
  path: string;
  expected: string | null;
  found: string | null;
  operationIndex?: number;
}

type ConflictCode =
  | "SOURCE_NOT_FOUND"
  | "SOURCE_CHANGED"
  | "DEST_EXISTS"
  | "DEST_NOT_FOUND"
  | "DEST_CHANGED"
  | "CAS_CONFLICT";
```

---

## Conflict Error Codes

| Code | Meaning |
|---|---|
| `SOURCE_NOT_FOUND` | Source file doesn't exist |
| `SOURCE_CHANGED` | Source file's blobId doesn't match expected |
| `DEST_EXISTS` | Destination exists when `basis: null` (expected empty) |
| `DEST_NOT_FOUND` | Destination doesn't exist when `basis: "blobId"` |
| `DEST_CHANGED` | Destination blobId doesn't match basis |
| `CAS_CONFLICT` | `commitFiles()` basis check failed |

---

## Troubleshooting

| Issue | Cause | Solution |
|---|---|---|
| Upload fails with 403 | Auth check failing | Verify `uploadAuth` returns `true` for authenticated users |
| Download fails with 403 | File deleted or blobId mismatch | Check file exists with `fs.stat()` |
| Files not appearing after upload | `commitFiles` not called | Ensure mutation runs after upload completes |
| Orphaned blobs accumulating | GC frozen or deployment torn down | Check `freezeGc` flag; manually clean dev storage zones |
| URL expires too quickly | `downloadUrlTtl` too short | Increase in ConvexFS constructor options |
| CDN returns 404 | Blob not yet replicated | Wait a moment; check storage zone replication status |
| `prefix` matching unexpected files | Missing trailing `/` | Use `/users/` not `/users` to avoid matching `/users-backup/` |

### Debug logging

```typescript
console.log("File stat:", await fs.stat(ctx, path));
console.log("List:", await fs.list(ctx, { paginationOpts: { numItems: 10, cursor: null } }));
```
