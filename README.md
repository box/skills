# Box Agent Skills

Agent Skills help AI assistants work with Box: find and organize content, support legal document workflows, and build or troubleshoot Box integrations. Each skill provides instructions for a specific kind of task, from client intake to SDK development and bulk operations.

The skills follow the [Agent Skills format](https://agentskills.io/) and can be installed individually or through a platform plugin. Installing instructions does not connect your assistant to Box; authorize Box access separately when your task needs it.

## Available Skills

| Skill | When to use it |
|---|---|
| [`box`](skills/box/SKILL.md) | Start with general Box tasks, choose the appropriate tools, and troubleshoot authentication or access errors. |
| [`box-cli`](skills/box-cli/SKILL.md) | Run Box CLI commands, automate shell workflows, and manage bulk operations and recovery. |
| [`box-mcp`](skills/box-mcp/SKILL.md) | Connect an assistant to Box's hosted MCP server, discover available tools, and complete Box content operations. |
| [`box-sdk-development`](skills/box-sdk-development/SKILL.md) | Build or debug application code using official Box SDKs, with guidance for Python and TypeScript/Node. |
| [`box-integration-best-practices`](skills/box-integration-best-practices/SKILL.md) | Review or improve Box integrations for efficient API calls, resumable bulk work, and rate-limit handling. |
| [`box-legal-workflows`](skills/box-legal-workflows/SKILL.md) | Supply shared guidance for the legal skills: collaboration roles, AI boundaries, and human review. |
| [`box-legal-workflows-intake`](skills/box-legal-workflows-intake/SKILL.md) | Check intake submissions against firm requirements, extract matter details, route attorney review, and prepare engagement letters. |
| [`box-legal-workflows-contract`](skills/box-legal-workflows-contract/SKILL.md) | Compare contracts with approved templates, produce cited variance reports, capture metadata, and monitor renewals or expirations. |
| [`box-legal-workflows-ma`](skills/box-legal-workflows-ma/SKILL.md) | Build and manage M&A data rooms, scope access, and answer due diligence questions with document citations. |

For application code, use `box-sdk-development`; for command-line work, use `box-cli`; for operations through a Box MCP connection, use `box-mcp`. Pair integration work with `box-integration-best-practices` when efficiency, bulk processing, or throttling matters.

The intake, contract, and M&A skills require both `box` and `box-legal-workflows`. The shared legal skill supports those workflows. Firms supply the checklists, templates, and review criteria; attorneys make legal and risk determinations.

## Installation

### Install individual skills

Choose skills interactively:

```bash
npx skills add box/skills
```

To install a specific skill, use its name from the catalog:

```bash
npx skills add box/skills --skill box-sdk-development
```

For a legal workflow, also install its required foundation skills. For example:

```bash
npx skills add box/skills --skill box
npx skills add box/skills --skill box-legal-workflows
npx skills add box/skills --skill box-legal-workflows-contract
```

Browse the [Box skills listing](https://skills.sh/box/skills) for installation options.

### Install a platform plugin

This repository also includes plugin configuration for supported platforms. Use the platform notes below, and follow the [Box MCP supported-platform guides](https://docs.box.com/en/box-mcp/supported-ai-platforms) to connect and authorize Box access. Some repository notes describe plugin metadata rather than a complete installation flow.

| Platform | Repository notes |
|---|---|
| Codex | [Codex notes](.codex-plugin/README.md) |
| Cursor | [Cursor installation and setup](.cursor-plugin/README.md) |
| Claude Code | [Claude Code plugin metadata](.claude-plugin/README.md) |
| Kiro | [Install as a Kiro Power](#kiro-power) |

For Box connection instructions for other clients, including Claude Cowork, see the supported-platform guides above.

The root `plugin.json` and `mcp.json` follow the [Agent Plugins specification](https://agent-plugins.org/specification). The MCP configuration declares `https://mcp.box.com`; your client manages authentication. Store credentials in your client's settings, outside this repository.

### Kiro Power

In Kiro, open the Powers panel and install the Box Power. If it is not shown in the catalog, select **Add Custom Power** → **Import power from GitHub** and enter `https://github.com/box/skills`.

Kiro is not listed in the [Box MCP supported-platform guides](https://docs.box.com/en/box-mcp/supported-ai-platforms). For a custom connection, follow the [Box MCP setup guide](https://developer.box.com/guides/box-mcp/setup) and your Kiro client's MCP configuration instructions. Confirm that the client supports the required authentication flow. Installing the Power and authorizing a Box connection are separate steps.

## Prerequisites

Requirements depend on the task:

| Task | Requirements |
|---|---|
| Install skills with `npx` | Node.js/npm and an agent that supports Agent Skills. |
| Work with Box through MCP | An authorized Box MCP connection. Available tools depend on admin settings, scopes, permissions, and product entitlements. |
| Run Box CLI operations | The [Box CLI](https://developer.box.com/guides/cli/) installed and authenticated for the intended Box user. |
| Develop SDK application code | The appropriate official Box SDK and runtime. Live API calls also need configured Box authentication. |
| Run legal workflows | The `box` and `box-legal-workflows` skills, Box access, and the firm's relevant checklist, template, or access criteria. Engagement letter generation also needs a registered Doc Gen template. |
| Use direct REST as an approved fallback | A valid Box access token with the required access. |

## Usage

Your agent uses installed skills when their descriptions match the task. You can name a skill explicitly when you want a particular workflow. Try these prompts, replacing folder references and firm materials with your own:

| Skill | Example prompt |
|---|---|
| `box` | “Help me diagnose a 403 error when accessing this Box folder.” |
| `box-cli` | “Use the Box CLI to list the first 20 items in folder 0 as JSON.” |
| `box-mcp` | “Use my Box MCP connection to find invoices in this folder.” |
| `box-sdk-development` | “Use the official Python Box SDK to add file upload to this application, following its existing authentication setup.” |
| `box-integration-best-practices` | “Review this Box sync worker for redundant API calls, retry behavior, and 429 handling.” |
| `box-legal-workflows` | “Explain the Box collaboration roles and human review steps used by the legal workflow skills.” |
| `box-legal-workflows-intake` | “Check this intake folder against our required-document checklist and summarize missing items for attorney review.” |
| `box-legal-workflows-contract` | “Compare this NDA with our approved template and produce a cited variance report for attorney review.” |
| `box-legal-workflows-ma` | “Set up an M&A data room using our folder template and access matrix.” |

## Verify Your Connection

Verify access through the tool you intend to use. A successful CLI or REST request does not verify your MCP connection or application SDK configuration.

- **MCP:** Ask your agent to discover the available tools and call `who_am_i` to confirm the intended Box user. Missing tools may require a client or administrator configuration change.
- **CLI:** Run the commands below to confirm the acting user and list a few root-folder items.
- **SDK:** Check the installed SDK version and use the application's configured client to make a small read request, such as retrieving the current user.

```bash
box users:get me --json
box folders:items 0 --json --max-items 5
```

For MCP tasks, perform operations and verification through the same connection. For explicit CLI tasks, use the CLI. For tasks without a specified tool, the `box` skill guides tool selection; direct REST requires an approved fallback and separate authentication.

<details>
<summary>Optional: verify direct REST access</summary>

Set `BOX_ACCESS_TOKEN` securely in your local session, then run:

```bash
curl -sS -H "Authorization: Bearer $BOX_ACCESS_TOKEN" \
  -H "Accept: application/json" \
  "https://api.box.com/2.0/users/me"
```

</details>

## Skill Structure

Each skill lives in `skills/<skill-name>/`:

- `SKILL.md` — Required manifest with a name, description, and task instructions.
- `references/` — Optional detailed guidance, such as SDK-specific instructions or Box capability references.

The `box-cli`, `box-mcp`, and `box-integration-best-practices` skills keep their instructions in a single `SKILL.md`. Other skills use references or guidance from related skills as needed. CLI syntax comes from installed command help; MCP tool names and inputs come from runtime discovery; SDK methods must match the installed SDK version.

## Contributing

Follow the [Agent Skills specification](https://agentskills.io/). Add or update a skill under `skills/`, with only the supporting resources its workflow requires. State any dependencies on other skills.

When adding or renaming a skill, update this README's catalog and example prompts, and review prerequisites and platform notes for affected guidance.

## License

[MIT](LICENSE)

## Privacy and Support

- [Box Privacy Policy](https://www.box.com/legal/privacypolicy)
- [Box Support](https://support.box.com/)
- [Repository issues](https://github.com/box/skills/issues) for problems with these skill instructions.
