# Bulk operations

Use this when work repeats across a collection. Many Box operations still require one request per item; a bounded, resumable loop can be the right design.

1. **Inventory:** paginate fully and record item IDs, version, size, actor, and intended action. Prefer markers when supported for large or changing collections; do not retain markers as permanent checkpoints. Check search limits before treating search results as complete inventory.
2. **Plan:** look for Box-side operations, supported batch endpoints, ZIP downloads, metadata queries, or event-driven changes that reduce requests. Estimate calls per item and total calls against endpoint rates and monthly allocation. Set an initial, configurable concurrency limit.
3. **Pilot:** run a representative, safe subset through the intended path. Measure calls per completed item, throughput, bytes, latency, and visible 429s. Verify any mutations, extrapolate full-job cost and duration, and adjust the plan if measurements exceed it.
4. **Execute and resume:** use a bounded queue and limiter shared by workers under the same actor. Let the SDK own its configured retries; slow new work when throttling appears. Persist each item's status, Box ID/version, destination, and error or request ID. Reconcile uncertain writes before retrying; resume only unfinished items.
5. **Reconcile:** compare results with the inventory and Box state. Report succeeded, skipped, and failed items separately, plus throughput and API usage against the plan.

Published limits are planning inputs; Box can apply other endpoint, enterprise, or temporary limits. [Box rate limits](https://developer.box.com/guides/api-calls/permissions-and-errors/rate-limits/).
