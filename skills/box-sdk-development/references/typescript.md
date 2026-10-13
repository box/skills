# TypeScript and Node SDK

- Check the installed `box-node-sdk` major and its declarations. v10 uses the generated interface; v4 combines legacy and generated interfaces for migration. The `box-typescript-sdk-gen` artifact predates v10. For a new project, pin the intended major explicitly: Box currently documents that the unversioned npm install resolves to v4.
- Import types and pass option objects as the installed declarations require. Check whether a call returns a stream, response wrapper, or parsed resource before consuming it; await stream completion and handle aborts.
- For browser code, verify SDK browser support and allowed CORS origins. Keep client secrets and service-account keys on the server; use an appropriate user-scoped flow in the browser.

Source: [Box Node SDK versions, installation, and browser support](https://github.com/box/box-node-sdk).
