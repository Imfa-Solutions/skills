---
name: convex-file-system
description: >
  ConvexFS (convex-fs) — path-based file storage and serving component for Convex powered by
  bunny.net CDN. Covers installation, setup, file upload/download flows, path management,
  blob lifecycle, atomic transactions (move/copy/delete), compare-and-swap, signed URLs,
  file expiration, garbage collection, auth for uploads/downloads, multiple filesystems,
  React integration, and production best practices. Use when working with ConvexFS, uploading
  files in Convex, serving files via CDN, managing file paths, building file storage features,
  or configuring bunny.net with Convex. Triggers on: convex-fs, ConvexFS, bunny.net, file upload,
  file storage convex, blob, commitFiles, registerRoutes, buildDownloadUrl, fs.stat, fs.list,
  fs.transact, fs.move, fs.copy, fs.delete, fs.writeFile, fs.getDownloadUrl, "how do I upload
  files in Convex", "serve files from Convex", "ConvexFS setup".
---

# ConvexFS — File Storage for Convex

> `convex-fs` — Path-based file storage with global CDN delivery via bunny.net.

## Installation & Setup

### 1. Install

```bash
npm install convex-fs
```

### 2. Register component

```typescript
// convex/convex.config.ts
import { defineApp } from "convex/server";
import fs from "convex-fs/convex.config.js";

const app = defineApp();
app.use(fs);
export default app;
```

### 3. Create ConvexFS instance

```typescript
// convex/fs.ts
import { ConvexFS } from "convex-fs";
import { components } from "./_generated/api";

export const fs = new ConvexFS(components.fs, {
  storage: {
    type: "bunny",
    apiKey: process.env.BUNNY_API_KEY!,
    storageZoneName: process.env.BUNNY_STORAGE_ZONE!,
    cdnHostname: process.env.BUNNY_CDN_HOSTNAME!,
    tokenKey: process.env.BUNNY_TOKEN_KEY, // recommended for signed URLs
  },
});
```

### 4. Register HTTP routes

```typescript
// convex/http.ts
import { httpRouter } from "convex/server";
import { registerRoutes } from "convex-fs";
import { components } from "./_generated/api";
import { fs } from "./fs";

const http = httpRouter();

registerRoutes(http, components.fs, fs, {
  pathPrefix: "/fs",
  uploadAuth: async (ctx) => {
    const identity = await ctx.auth.getUserIdentity();
    return identity !== null;
  },
  downloadAuth: async (ctx, blobId, path) => {
    const identity = await ctx.auth.getUserIdentity();
    return identity !== null;
  },
});

export default http;
```

Creates: `POST /fs/upload` (upload proxy) and `GET /fs/blobs/{blobId}` (302 redirect to CDN).

### 5. Environment variables

Set in Convex dashboard (Settings → Environment Variables):

```
BUNNY_API_KEY=your-api-key
BUNNY_STORAGE_ZONE=your-storage-zone-name
BUNNY_CDN_HOSTNAME=your-zone.b-cdn.net
BUNNY_TOKEN_KEY=your-token-auth-key
BUNNY_REGION=ny  # Optional: ny, la, sg, uk, se, br, jh, syd (default: Frankfurt)
```

## Core Concepts

### Architecture

```
React Client ──▶ Convex Backend (ConvexFS) ──▶ bunny.net CDN (File Storage + Edge)
```

- **File metadata** (paths, content types, sizes) → Convex tables
- **File contents** (blobs) → bunny.net Edge Storage

### Paths

Any UTF-8 string. `list()` uses **prefix matching** (not directory listing):
- `prefix: "/users"` matches `/users/alice.txt` AND `/users-backup/data.bin`
- `prefix: "/users/"` matches only under `/users/`

### Blob lifecycle

```
Upload → Pending (4h TTL) → Committed (refCount=1) → Deleted (refCount=0) → GC cleanup
```

- **Reference counting**: copy increments refCount (zero-copy), delete decrements it.
- **Garbage collection**: 3 automatic jobs — upload GC (hourly), blob GC (hourly), file expiration GC (every 15s).
- **Grace period**: orphaned blobs retained for `blobGracePeriod` (default 24h) before permanent deletion.

### Attributes

```typescript
interface FileAttributes {
  expiresAt?: number; // Unix timestamp — auto-deleted by FGC
}
```

Attributes are **path-specific**: cleared on move, not inherited on copy, removed on overwrite.

## Core API

### Query methods

```typescript
// Get file metadata by path
const file = await fs.stat(ctx, "/uploads/photo.jpg");
// Returns: { path, blobId, contentType, size, attributes } | null

// List files with pagination
const result = await fs.list(ctx, {
  prefix: "/uploads/",
  paginationOpts: { numItems: 50, cursor: null },
});
// Returns: { page: FileMetadata[], continueCursor, isDone }
```

### Mutation methods

```typescript
// Commit uploaded blobs to paths
await fs.commitFiles(ctx, [
  { path: "/file.txt", blobId: "uuid-here" },                     // overwrite if exists
  { path: "/new.txt", blobId: "uuid", basis: null },               // FAIL if exists
  { path: "/update.txt", blobId: "new-uuid", basis: "old-uuid" },  // CAS: fail if changed
]);

// Atomic multi-operation transaction
await fs.transact(ctx, [
  { op: "move", source: file, dest: { path: "/new/path.txt" } },
  { op: "copy", source: file, dest: { path: "/backup.txt", basis: null } },
  { op: "delete", source: file },
  { op: "setAttributes", source: file, attributes: { expiresAt: Date.now() + 3600000 } },
]);

// Convenience methods
await fs.move(ctx, "/old.txt", "/new.txt");  // throws if source missing or dest exists
await fs.copy(ctx, "/a.txt", "/b.txt");      // throws if source missing or dest exists
await fs.delete(ctx, "/file.txt");           // idempotent (no-op if missing)
```

**Basis values** (for `commitFiles` and `transact` destinations):

| Value | Meaning |
|---|---|
| `undefined` | No check — overwrite if exists |
| `null` | File must NOT exist |
| `"blobId"` | File's current blobId must match (compare-and-swap) |

### Action methods

```typescript
// Generate signed download URL
const url = await fs.getDownloadUrl(ctx, blobId, { extraParams: { filename: "doc.pdf" } });

// Download blob data
const data = await fs.getBlob(ctx, blobId); // ArrayBuffer | null

// Download file contents + metadata
const result = await fs.getFile(ctx, "/file.txt"); // { data, contentType, size } | null

// Upload data and get blobId
const blobId = await fs.writeBlob(ctx, imageData, "image/webp");

// Upload + commit in one call
await fs.writeFile(ctx, "/report.pdf", pdfData, "application/pdf");
```

### Client utilities

```typescript
import { buildDownloadUrl } from "convex-fs";

const url = buildDownloadUrl(siteUrl, "/fs", file.blobId, file.path, { filename: "doc.pdf" });
```

## Upload & Serve Flow

### Upload (React)

```tsx
const handleUpload = async (file: File) => {
  const siteUrl = (import.meta.env.VITE_CONVEX_URL ?? "").replace(/\.cloud$/, ".site");

  // 1. Upload blob
  const res = await fetch(`${siteUrl}/fs/upload`, {
    method: "POST",
    headers: { "Content-Type": file.type },
    body: file,
  });
  const { blobId } = await res.json();

  // 2. Commit to path (via mutation)
  await commitFile({ blobId, filename: file.name });
};
```

### Serve (React)

```tsx
import { buildDownloadUrl } from "convex-fs";

function Image({ path }: { path: string }) {
  const file = useQuery(api.files.getFile, { path });
  const siteUrl = (import.meta.env.VITE_CONVEX_URL ?? "").replace(/\.cloud$/, ".site");

  if (!file) return <div>Loading...</div>;
  const url = buildDownloadUrl(siteUrl, "/fs", file.blobId, file.path);
  return <img src={url} alt={path} />;
}
```

## Security Rules

1. **Always authenticate uploads** — open upload endpoints are a serious risk.
2. **Use path-based authorization** — validate path ownership in `downloadAuth`.
3. **Enable token authentication** on bunny.net — prevents URL tampering.
4. **Set appropriate URL TTLs**: sensitive content 60–300s, general 3600s (default), streaming 3600s+.

## Conflict Handling

```typescript
import { ConvexError } from "convex/values";
import { isConflictError } from "convex-fs";

try {
  await fs.commitFiles(ctx, files);
} catch (e) {
  if (e instanceof ConvexError && isConflictError(e.data)) {
    // e.data: { code, path, expected, found }
    // Codes: SOURCE_NOT_FOUND, SOURCE_CHANGED, DEST_EXISTS, DEST_NOT_FOUND, DEST_CHANGED, CAS_CONFLICT
  }
}
```

## Constructor Options

| Option | Type | Default | Description |
|---|---|---|---|
| `storage` | `StorageConfig` | required | Bunny.net backend config |
| `downloadUrlTtl` | `number` | `3600` | Signed URL expiration (seconds) |
| `blobGracePeriod` | `number` | `86400` | Orphaned blob retention (seconds) |

## Reference Files

- **Patterns & examples**: User files, temp files, atomic ops, CAS updates, retry, pagination, React hooks → See [references/examples.md](references/examples.md)
- **Advanced topics**: Multiple filesystems, disaster recovery, testing, GC details, Bunny.net setup, TypeScript types, troubleshooting → See [references/advanced.md](references/advanced.md)
