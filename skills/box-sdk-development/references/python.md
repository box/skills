# Python SDK

- Check the installed `boxsdk` distribution and import path. Box SDK v10 exposes the generated `box_sdk_gen` interface; v4 includes both `boxsdk` and `box_sdk_gen` for gradual migration. Preserve the project's major version unless migration is in scope. Do not mix legacy `Client.file(...)` calls with generated client methods.
- Inspect the installed method signature and return type, including request option objects and stream ownership. Python examples for another SDK major may have different call shapes.
- For local downloads, close the response and destination on failure; publish a completed file only after the transfer succeeds. Verify this with an interrupted-transfer test when implementing download code.

Source: [Box Python SDK versions and usage](https://github.com/box/box-python-sdk).
