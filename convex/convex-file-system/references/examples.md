# ConvexFS Patterns & Examples

## Table of Contents

- [Basic File Upload Mutation](#basic-file-upload-mutation)
- [User-Specific Files](#user-specific-files)
- [Temporary Files with Expiration](#temporary-files-with-expiration)
- [Atomic File Operations](#atomic-file-operations)
- [Compare-and-Swap Update](#compare-and-swap-update)
- [Retry on Conflict](#retry-on-conflict)
- [Custom Download Filenames](#custom-download-filenames)
- [React File Upload Component](#react-file-upload-component)
- [React Paginated File List](#react-paginated-file-list)

---

## Basic File Upload Mutation

```typescript
// convex/files.ts
import { mutation } from "./_generated/server";
import { v } from "convex/values";
import { fs } from "./fs";

export const commitFile = mutation({
  args: { blobId: v.string(), filename: v.string() },
  handler: async (ctx, args) => {
    const path = `/uploads/${args.filename}`;
    await fs.commitFiles(ctx, [{ path, blobId: args.blobId }]);
    return { path };
  },
});
```

---

## User-Specific Files

```typescript
export const uploadUserFile = mutation({
  args: { blobId: v.string(), filename: v.string() },
  handler: async (ctx, args) => {
    const identity = await ctx.auth.getUserIdentity();
    if (!identity) throw new Error("Not authenticated");

    const path = `/users/${identity.subject}/${args.filename}`;
    await fs.commitFiles(ctx, [{ path, blobId: args.blobId }]);
    return { path };
  },
});

export const getUserFiles = query({
  args: { paginationOpts: paginationOptsValidator },
  handler: async (ctx, args) => {
    const identity = await ctx.auth.getUserIdentity();
    if (!identity) throw new Error("Not authenticated");

    return await fs.list(ctx, {
      prefix: `/users/${identity.subject}/`,
      paginationOpts: args.paginationOpts,
    });
  },
});
```

---

## Temporary Files with Expiration

```typescript
export const createTempFile = mutation({
  args: {
    blobId: v.string(),
    filename: v.string(),
    expiresInSeconds: v.number(),
  },
  handler: async (ctx, args) => {
    const path = `/temp/${args.filename}`;
    const expiresAt = Date.now() + args.expiresInSeconds * 1000;

    await fs.commitFiles(ctx, [{
      path,
      blobId: args.blobId,
      attributes: { expiresAt },
    }]);
    return { path };
  },
});
```

The FGC (File GC) job runs every 15 seconds and deletes expired files automatically.

---

## Atomic File Operations

Process a file and atomically create a backup:

```typescript
export const processAndArchive = action({
  args: { path: v.string() },
  handler: async (ctx, args) => {
    const file = await fs.stat(ctx, args.path);
    if (!file) throw new Error("File not found");

    // Download and process
    const data = await fs.getFile(ctx, args.path);
    const processed = await resizeImage(data!.data);

    // Upload processed version
    const newBlobId = await fs.writeBlob(ctx, processed, "image/webp");

    // Atomically move original to backup
    await fs.transact(ctx, [
      {
        op: "move",
        source: file,
        dest: { path: `${args.path}.backup`, basis: null },
      },
      {
        op: "setAttributes",
        source: { ...file, path: `${args.path}.backup` },
        attributes: { expiresAt: Date.now() + 7 * 24 * 60 * 60 * 1000 },
      },
    ]);

    // Commit new version (CAS: only if original hasn't changed)
    await fs.commitFiles(ctx, [{
      path: args.path,
      blobId: newBlobId,
      basis: file.blobId,
    }]);
  },
});
```

---

## Compare-and-Swap Update

Only update if the file hasn't changed since you read it:

```typescript
export const safeUpdate = mutation({
  args: { path: v.string(), newBlobId: v.string() },
  handler: async (ctx, args) => {
    const file = await fs.stat(ctx, args.path);

    await fs.commitFiles(ctx, [{
      path: args.path,
      blobId: args.newBlobId,
      basis: file?.blobId ?? null, // null = must not exist, string = must match
    }]);
  },
});
```

---

## Retry on Conflict

```typescript
import { ConvexError } from "convex/values";
import { isConflictError } from "convex-fs";

export const robustUpdate = mutation({
  args: { path: v.string(), newBlobId: v.string() },
  handler: async (ctx, args) => {
    for (let attempt = 0; attempt < 3; attempt++) {
      const file = await fs.stat(ctx, args.path);
      try {
        await fs.commitFiles(ctx, [{
          path: args.path,
          blobId: args.newBlobId,
          basis: file?.blobId ?? null,
        }]);
        return; // success
      } catch (e) {
        if (e instanceof ConvexError && isConflictError(e.data)) continue;
        throw e;
      }
    }
    throw new Error("Failed after max retries");
  },
});
```

---

## Custom Download Filenames

Requires a bunny.net Edge Rule:
- **Action**: Set Response Header → `Content-Disposition` → `attachment; filename="%{Query.filename}"`
- **Condition**: Query String → `?filename=*` OR `*&filename=*`

```typescript
export const getDownloadUrl = query({
  args: { path: v.string() },
  handler: async (ctx, args) => {
    const siteUrl = process.env.CONVEX_SITE_URL!;
    const file = await fs.stat(ctx, args.path);
    if (!file) return null;

    const filename = args.path.split("/").pop() ?? "download";
    return buildDownloadUrl(siteUrl, "/fs", file.blobId, args.path, { filename });
  },
});
```

---

## React File Upload Component

```tsx
import { useState } from "react";
import { useMutation } from "convex/react";
import { api } from "../convex/_generated/api";

function FileUpload() {
  const [uploading, setUploading] = useState(false);
  const commitFile = useMutation(api.files.commitFile);

  const siteUrl = (import.meta.env.VITE_CONVEX_URL ?? "").replace(/\.cloud$/, ".site");

  const handleUpload = async (e: React.ChangeEvent<HTMLInputElement>) => {
    const file = e.target.files?.[0];
    if (!file) return;

    setUploading(true);
    try {
      const res = await fetch(`${siteUrl}/fs/upload`, {
        method: "POST",
        headers: { "Content-Type": file.type },
        body: file,
      });
      const { blobId } = await res.json();
      await commitFile({ blobId, filename: file.name });
      alert("Upload complete!");
    } catch (err) {
      alert("Upload failed: " + (err as Error).message);
    } finally {
      setUploading(false);
    }
  };

  return <input type="file" onChange={handleUpload} disabled={uploading} />;
}
```

---

## React Paginated File List

```tsx
import { usePaginatedQuery } from "convex-fs/react";
import { api } from "../convex/_generated/api";

function FileList() {
  const { results, status, loadMore } = usePaginatedQuery(
    api.files.listFiles,
    { prefix: "/uploads/" },
    { initialNumItems: 20 },
  );

  return (
    <div>
      {results.map((file) => (
        <div key={file.path}>{file.path} ({file.size} bytes)</div>
      ))}
      {status === "CanLoadMore" && (
        <button onClick={() => loadMore(20)}>Load more</button>
      )}
    </div>
  );
}
```
