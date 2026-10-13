---
name: box-cli
description: Use the official Box CLI for Box command-line operations, reproducible shell automation, CLI authentication or troubleshooting, and bulk Box work. Do not use for generic shell questions, Box strategy, or application code that should use a Box SDK.
---

# Box CLI

Use the installed Box CLI to complete and verify Box operations.

## Operating workflow

1. Identify the requested outcome, target, and actor. If the request leaves a risky target, recipient, role, impersonated user, or batch scope ambiguous, ask one focused question before running a command.
2. Confirm the intended CLI environment and acting user before the operation. If the user names an environment, inspect `box configure:environments:select --help` and select that environment before checking identity:

   ```bash
   box users:get me --json --fields id,name,login
   ```

   Include the intended `--as-user <USER_ID>` in the identity check when impersonation is authorized. `users:get me` confirms the acting user, not the environment name. Reuse the same environment and actor controls throughout the operation and its verification; repeat the check after any authorized environment or actor change. Never switch to a more privileged identity merely to bypass an error.
3. Resolve human-readable names to stable Box IDs before mutation. If multiple items match, present the candidates and ask the user to choose.
4. Prefer a dedicated CLI command. Add `--json` for machine-readable output and narrow `--fields` to the data needed for the task.
5. Verify mutations with a read using the same actor. For deletes, check the parent collection or trash as appropriate; a `404` alone can also mean the actor lacks access. For asynchronous operations, report pending status until completion is confirmed.
6. Report the actor, action, relevant object IDs, verification evidence, and any unresolved failure. Include commands when they help the user reproduce the result.

## Common patterns

Record `box --version`. Inspect command help when syntax is unfamiliar, the installed version differs from the verified version below, or a command is rejected. Resolve uncertainty before running a write.

```bash
# Identify the actor
box users:get me --json --fields id,name,login

# Inspect the root folder and a bounded set of children
box folders:get 0 --json --fields id,name
box folders:items 0 --json --fields id,type,name --max-items 20

# Search and preserve parent information for disambiguation
box search "Launch Plan" --json --fields id,type,name,parent --limit 20

# Read an item by ID
box files:get <FILE_ID> --json --fields id,name,parent,owned_by
box folders:get <FOLDER_ID> --json --fields id,name,parent,owned_by

# Create, then verify the returned folder ID
box folders:create <PARENT_FOLDER_ID> "Approved Assets" --json
box folders:get <RETURNED_FOLDER_ID> --json --fields id,name,parent
```

Box uses `0` as the root folder ID. A path or item name is not a stable identifier.

Use `box <topic>:<command> --help` to check flags and argument order in the installed version.

## Setup and authentication

Only enter setup mode when the CLI is absent, authentication fails, or the user asks for it.

```bash
npm install --global @box/cli
box login -d
```

- `box login -d` uses the official Box CLI application for interactive content operations.
- In a headless environment, have the user run `box login -d --code` in their own terminal for the official CLI app, or `box login --platform-app --code` for their custom app. `--code` is a boolean flag: the CLI displays an authorization URL, then prompts for the state and authorization code after the user authorizes in an external browser. Enter those values only at the local CLI prompts.
- Use `box login --platform-app` when the user needs a custom OAuth application or scopes beyond the official CLI application.
- For approved server automation with JWT or Client Credentials Grant, use the current `box configure:environments:add --help` flow and protect the configuration file outside source control.
- Developer tokens are for short local tests. Do not embed tokens, client secrets, private keys, or authorization codes in commands, prompts, logs, fixtures, or repository files.

After setup, verify the identity with `box users:get me`; do not print credential stores or raw environment configuration.

## Risky and destructive work

The request must explicitly authorize the exact scope before:

- deleting or permanently deleting content;
- creating, changing, or removing collaborations or shared links;
- impersonating another user;
- changing enterprise users, groups, retention, or governance settings; or
- running a broad batch that can alter many objects.

If that authorization is absent, show the actor, target IDs, proposed role or change, and batch size, then ask for confirmation. Do not rely on CLI prompts because some destructive commands do not prompt.

Run dependent writes serially. Serialize processes sharing an OAuth environment to avoid overlapping token refreshes or environment changes. Verify using returned IDs.

## Bulk operations and recovery

Use CSV or JSON input through `--bulk-file-path` only when the specific command supports that flag. Check the command's help and official bulk examples for supported columns and nested values. Before executing a batch:

1. Validate input columns against the command's arguments and flags. Row values override command-line values, including `as-user`; inspect actor and target overrides before execution.
2. Validate a small representative subset when the operation is reversible and the user has authorized it.
3. Keep a reconciliation record separate from the CLI input, with source-row number or key, intended target ID, actor, and outcome. Do not add tracking columns the command does not support.
4. Capture output and per-row error diagnostics. Bulk execution can continue after row failures and finish without a nonzero exit status; reconcile each row before reporting success.

For partial failures:

- Treat successful rows as committed until verification proves otherwise.
- Retry `429` responses only after the indicated delay.
- Retry timeouts or `5xx` responses only when the operation is idempotent or a read proves the mutation did not occur.
- Do not retry `401`, `403`, or ambiguous writes unchanged. Correct identity, permission, or state first.
- Retry only the verified-safe failed subset, then reconcile every source row against Box.

Never restart an entire partially successful mutation batch merely because some rows failed.

## REST fallback

When no dedicated command exists, prefer `box request` because it reuses the selected CLI identity:

```bash
box request /folders/0/items --query "limit=5" --json
```

Inspect `box request --help` before a write. Use explicit method, body, and query values, then verify the resulting Box object through a read. Raw `curl` with a bearer token is outside the normal workflow and should be used only when the user supplies an approved secure authentication mechanism.

## Current public sources

Use these sources when installed help is insufficient:

- [Box CLI repository and installation](https://github.com/box/boxcli)
- [Box CLI authentication](https://github.com/box/boxcli/blob/main/docs/authentication.md)
- [Box CLI command documentation](https://github.com/box/boxcli/tree/main/docs)
- [Box Developer CLI guides](https://developer.box.com/guides/cli/)

Examples and authentication flags checked on 2026-10-13 against the public Box CLI source and docs at commit `97e4351cb5e4a2077f37090bb5aca0ae2fe7020f` (package version `4.10.1`).
