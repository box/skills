# Python SDK

- Inspect `pyproject.toml` or requirements, lockfile, and installed `boxsdk`/`box_sdk_gen` symbols. Python SDK v10 uses the generated `box_sdk_gen` interface for new applications; v4 offers a coexistence path for older `boxsdk` code. Avoid copying legacy `Client.file(...).content()` examples into a v10 client without verifying the method.
- Check the installed method signature and object types with package source, type hints, or `inspect.signature`, then confirm behavior in the official SDK/API reference. Pay attention to optional request objects, stream ownership, and whether methods are synchronous or asynchronous.
- Reuse the configured client, token management, and any existing application wrapper. For downloads that create local files, stream to a temporary destination, close resources, and atomically mark a file complete only after the transfer succeeds.
- For bulk jobs, use a bounded worker pool with a limiter shared across workers for the same actor. Persist item outcomes and checkpoints, and keep retry budgets finite. Do not use an unbounded executor over the collection.
- Run the project's type/lint/test commands and focused tests for method arguments, multiple pages, 429 handling, partial download cleanup, and safe resume. Use an authorized Box test account for integration verification when available.

Source: [Python SDK versions and usage](https://github.com/box/box-python-sdk).
