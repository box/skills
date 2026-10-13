---
name: box-sdk-development
description: Build or debug application code with an official Box SDK, especially TypeScript/Node or Python. Use for authentication, file transfer, bulk jobs, rate limits, and SDK method correctness; not for one-off Box CLI or MCP content operations.
---

# Box SDK development

Build working Box integrations that match the project's installed SDK and remain observable and efficient at production scale. Preserve an existing Box client or router abstraction when it serves the task; improve it when it causes repeated calls, hides results, or prevents rate-limit coordination.

## Before writing code

1. Inspect the project language, SDK package and locked version, authentication flow, runtime (browser, server, worker), existing Box client or router, and the requested data volume. Keep the project's supported SDK major version unless migration is requested or required.
2. For **each Box operation used**, verify the exact method, argument order, request object fields, return type, pagination mode, and error shape against the installed SDK's types/source and the matching official SDK/API reference. Do not translate method signatures from another SDK version or language by memory. If documentation and installed types disagree, code to the installed version and explain the mismatch.
3. Map the operation plan before implementation: target actor and permissions, API calls per item and per page, likely request rate and total allocation, data transfer size, restart behavior, and expected completion time. Ask only for missing product choices that materially change the design.
4. Implement the smallest suitable design, then run the project's type checker or compile step and focused tests. Verify actual SDK calls with a mock, fixture, or authorized sandbox integration where practical. A successful compile alone does not prove permissions, content, or throughput.

For TypeScript/Node, read [TypeScript guidance](references/typescript.md). For Python, read [Python guidance](references/python.md). For jobs spanning hundreds or more items, read [bulk operations](references/bulk-operations.md). For downloads or uploads, read [file transfer](references/file-transfer.md). Apply the shared rules below on every path.

## Design rules

- Choose the API for the desired **artifact**. For original file bytes outside Box, use the file content download operation or a ZIP download when grouping many complete files is appropriate. Metadata, previews, thumbnails, representations, and paginated item listings serve different purposes. Never reconstruct an original file from pages of representations.
- Treat listing as discovery, not transfer. Request only fields needed for the workflow, paginate every collection to its actual end, and use marker pagination when the endpoint supports it and the collection can exceed offset limits. Do not infer that one page, a high limit, or an empty page implies completion without checking the endpoint's pagination contract.
- Prefer server-side operations, such as copy, move, metadata query, or search, when the goal stays in Box. Avoid downloading and re-uploading bytes to perform a Box-internal change. Use events or webhooks plus catch-up where repeated full scans would be wasteful.
- Reuse the configured SDK client and its token cache rather than requesting a new access token per item. Count authentication and verification calls in the request budget; Box counts custom application calls toward enterprise API allocation even when they do not trigger 429s.
- For many original files, evaluate ZIP download against individual streaming. ZIP can reduce request overhead, but its item/size limits, archive assembly, failure reporting, and downstream need for per-file handling can make bounded individual downloads the better choice. A 25,000-file job needs partitioning and resumability either way.
- A loop is not inherently wrong: many Box mutations have no single bulk SDK method. Make the loop a bounded job with pagination, concurrency control, checkpoints, and reconciliation. Never unleash an unbounded `Promise.all`, thread pool, or request loop.
- Check the installed SDK's retry behavior before adding application retries. Current Box SDKs retry 429s with backoff and `Retry-After`; let that layer own those retries, and use a shared limiter to slow admission of new work when throttling is observed. For older SDKs or custom HTTP clients, implement one bounded retry layer that honors `Retry-After`. Handle other transient failures within that same retry budget. Retry ambiguous writes only after checking whether they succeeded; treat 401/403 and ordinary 400/404 as diagnosis, not retry targets.
- Keep tokens and private keys in the platform's secret store or environment, choose least privilege and the intended Box actor, and never expose credentials, original file content, signed download URLs, or sensitive metadata in logs. A browser must not receive server credentials.

## Observability is part of the feature

Instrument the Box client or existing router once so every operation can emit structured results without scattering logging across call sites. Capture **successes and failures**: operation, Box item ID/type, actor or tenant identifier where safe, status/error code, Box request ID when available, duration, bytes transferred where relevant, and job/correlation ID. Capture attempt count, 429s, and retry delay from SDK hooks or transport instrumentation when exposed; do not report these as zero merely because internal SDK retries are hidden. For bulk jobs, persist per-item outcome and checkpoint state durably; emit aggregate counts, throughput, API calls against the planned budget, and estimated remaining work during execution. Make alerts or operator-visible warnings fire when throttling, retry time, or total usage rises beyond the plan, rather than relying on a later Box report. Keep log volume, retention, and sensitive fields configurable; avoid printing a line for every successful item to the console when a durable ledger and metrics provide the needed traceability.

## Completion report — every finished SDK task

Return a short report with:

1. **What changed:** feature or fix, files touched, and verification performed or remaining.
2. **Design choices:** each material choice and its concrete reason, including the selected SDK/API operation, transfer method, pagination and batch strategy, retry/concurrency policy, and logging approach when relevant. State the expected API-call shape and scale assumptions for bulk work.
3. **Other viable options:** concise alternatives with the condition under which each is preferable and its cost. For a download feature, compare original-file streaming with ZIP download where supported; mention representations only if the requested output is a preview or rendition. Do not present an option that cannot meet the user's requirement as equivalent.

Keep this report proportional to the task. Explain uncertainty or unverified behavior plainly; never claim a one-shot guarantee when the runtime or Box permissions were not verified.

## Current official sources

Use current docs for live SDK signatures, feature support, and limits:

- [Box Node SDK](https://github.com/box/box-node-sdk) and [Box Python SDK](https://github.com/box/box-python-sdk)
- [Box API reference](https://developer.box.com/reference/) and [rate limits](https://developer.box.com/guides/api-calls/permissions-and-errors/rate-limits/)
- [Marker pagination](https://developer.box.com/guides/api-calls/pagination/marker-based/) and [ZIP download API](https://developer.box.com/reference/post-zip-downloads/)
