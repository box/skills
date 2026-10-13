# File transfer

Do not reconstruct original bytes from representation pages.

## Downloads

For original bytes, use the SDK's file-content download. Check redirect and stream behavior in the installed version. Stream with bounded memory; for local output, use a temporary file and publish only after completion. For an inventoried file, pin the download to its version when supported or detect changes before marking success. Verify byte count and Box's SHA-1 when available.

When an archive meets the requirement, consider ZIP to reduce per-file requests. Box currently limits a ZIP to the account upload limit or 10,000 files, whichever comes first, and recommends at most 25 GB; partition by count and size. Start streaming promptly because the URL expires within seconds. Status becomes available after download starts; check final skipped counts, archive contents, and name conflicts. ZIP selects item IDs, not fixed versions, so use version-pinned individual downloads when a stable snapshot is required. Treat ZIP URLs as secrets. Recheck these limits and behavior in current docs.

For browser delivery, consider an authorized direct download when it avoids proxying bytes through the server and fits the app's auth and user experience.

## Uploads

Choose direct or SDK-supported chunked upload by current size limits and resume needs. Stream the source; verify chunk commit and handle name conflicts or version updates intentionally.

Sources: [file download](https://developer.box.com/reference/get-files-id-content/), [ZIP download](https://developer.box.com/reference/post-zip-downloads/), [uploads](https://developer.box.com/guides/uploads/).
