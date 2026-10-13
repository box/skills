# File transfer decisions

## Original file outside Box

Use the SDK's file-content download operation to stream original bytes. Stream to a temporary file or the destination service; avoid loading a large file into memory. Check whether the SDK handles the Box download redirect, stream errors, timeouts, and cleanup. When exporting a previously inventoried file, pin its version with the content endpoint's `version` parameter if the installed SDK supports it, or detect a version change and reconcile before marking success. Compare downloaded byte count and Box's SHA-1 when available, then atomically publish the completed file. Do not create previews or iterate representation pages to obtain the source file.

For multiple complete files, check the ZIP download API and the installed SDK's support when an archive meets the requested result. It can reduce per-file API requests. The current API caps an archive at the account's upload limit or 10,000 files, whichever comes first, and recommends staying at or below 25 GB for reliable transfers. Partition by both count and size when the collection exceeds either bound. Start streaming immediately after creation: the returned download URL expires in seconds, and the status endpoint works only after the download starts. Check final status, skipped counts, archive contents, and name-conflict mappings before marking items complete. ZIP requests select item IDs rather than fixed file versions, so use version-pinned individual downloads when a stable snapshot is required. Treat ZIP URLs as secrets. If files need independent processing, incremental updates, individual retry, or original folder/name mapping that ZIP complicates, choose bounded streaming downloads instead. Recheck limits and URL behavior against current docs when implementing.

If the destination is a user's browser, consider whether a Box-authorized direct download can avoid relaying bytes through the application server. Select it only when the authorization, URL lifetime, and UX fit. If the requested artifact is a preview, use the appropriate preview or representation endpoint instead of original bytes.

## Uploads

Choose direct upload or an SDK-supported chunked upload based on current size limits and restart needs. Stream from the source. For chunked sessions, persist session/part progress where the API permits and verify commit before treating an upload as complete. Handle same-name conflicts and version uploads intentionally; never silently overwrite or create a duplicate.

## Estimate the request budget

Count discovery pages, content requests or ZIP jobs, status polls, retries, metadata lookups, and verification reads. Compare alternatives by request count, bytes moved, peak memory, failure isolation, archive limits, and time to resume. State these assumptions in the final report for large transfers.

Sources: [download file API](https://developer.box.com/reference/get-files-id-content/), [ZIP download API](https://developer.box.com/reference/post-zip-downloads/), [upload guide](https://developer.box.com/guides/uploads/).
