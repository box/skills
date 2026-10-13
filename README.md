# Box Agent Skills

Agent Skills to help developers using AI agents work with Box. Whether you're building Box integrations in code, working with Box content via MCP tools, configuring webhooks, or using Box AI retrieval — this plugin gives your assistant the context it needs to do it right.

The skills in this repo follow the [Agent Skills](https://agentskills.io/) format and can also be installed as a plugin for platforms like Codex, [Cursor](https://cursor.com), [Claude](https://claude.com), including Claude Code and Cowork, and [Kiro](https://kiro.dev).

## Installation

### As an Agent Skill

```bash
# Choose skills interactively
npx skills add box/skills

# Or install the SDK development skill independently
npx skills add box/skills --skill box-sdk-development
npx skills add box/skills --skill box-cli
npx skills add box/skills --skill box-mcp
```

Check out the latest and full list of skills [here](https://skills.sh/box/skills).

### As a Platform Plugin

This repo can also be installed as a plugin for supported platforms. MCP connection and OAuth setup vary by platform — see the setup guide for your platform below.

The root `plugin.json` and `mcp.json` follow the [Agent Plugins v1.0.0 specification](https://agent-plugins.org/specification). The portable MCP configuration declares the Box MCP endpoint; authentication remains managed by each client. Do not add credentials to this repository's root `mcp.json`; configure them only in your client's settings.

| Platform | Setup guide |
|---|---|
| Codex | [`.codex-plugin/README.md`](.codex-plugin/README.md) |
| Cursor | [`.cursor-plugin/README.md`](.cursor-plugin/README.md) |
| Claude Code | [`.claude-plugin/README.md`](.claude-plugin/README.md) |
| Kiro | [Install as a Kiro Power](#kiro-power) |

### Kiro Power

In Kiro, open the Powers panel and install the Box Power. If it is not shown in the catalog, select **Add Custom Power** → **Import power from GitHub** and enter `https://github.com/box/skills`. The bundled skills work in both Kiro IDE and CLI.

Note that the Box MCP Server currently does not support Dynamic Client Registration, so credential-free Power authentication is not yet available in Kiro IDE. Kiro CLI users can configure Box OAuth client credentials in their user MCP settings; future managed OAuth integration or confidential-client OAuth support in the IDE can enable the bundled MCP connection there.

Try it with: `Use the Box Power to add Box file upload to this app.`

## Available Skills

| Skill | Purpose |
|---|---|
| `box` | General Box foundation and router |
| `box-cli` | Reliable Box CLI operations, automation, bulk work, and recovery |
| `box-mcp` | Hosted Box MCP setup, tool discovery, and verified operations |
| `box-legal-workflows` | Shared legal workflow guidance |
| `box-legal-workflows-intake` | Legal intake and onboarding |
| `box-legal-workflows-contract` | Contract review and monitoring |
| `box-legal-workflows-ma` | M&A virtual data rooms |

## Usage

Skills are automatically available once installed. The agent will use them when relevant tasks are detected. Here are some example prompts:

### Connect and operate through Box MCP

`Connect this agent to Box using the hosted MCP server`

`Find invoices in this Box folder using the available MCP tools`

`Diagnose why Box AI tools are missing from this MCP connection`

### Implement Box content workflows

`Add Box file upload to my app`

`Create a shared link for this folder`

`Set up Box webhooks for new file events`

### Build document-driven flows

`Search my Box account for invoices`

`Use Box AI to classify documents`

`Wire webhooks to process new uploads`

### Troubleshoot integrations

`Debug 401 errors with my Box JWT auth`

`Fix webhook signature verification`

### Build efficient integrations

The [Box integration best practices skill](skills/box-integration-best-practices/SKILL.md) helps agents build and review repeatable Box API integrations, apps, and agent workflows.

`Review this Box sync worker for redundant API calls and 429 handling`

`Implement a resumable bulk metadata update that respects Box rate limits`

## Skill Structure

The skills follow the [Agent Skills Open Standard](https://agentskills.io/). Each skill owns its instructions and can be installed independently.

- `SKILL.md` - Required manifest and task guidance
- `references/` - Optional skill-local detail for substantial conditional workflows
- `scripts/` or `assets/` - Optional executable helpers or output templates

`box-cli` and `box-mcp` each contain their workflow in one `SKILL.md`. Use `box-cli` for CLI requests and `box-mcp` for MCP setup or operations through a Box MCP connection. Command details come from installed CLI help; MCP capabilities and inputs come from runtime tool discovery.

## Prerequisites

- **Box MCP connection** (for `box-mcp`) — Configure and authorize the connection using your [client's setup guide](https://docs.box.com/en/box-mcp/supported-ai-platforms). Installing the skill does not authenticate the client or enable admin-disabled tools.
- **Box CLI** (optional) — Install from [developer.box.com/guides/cli](https://developer.box.com/guides/cli) for CLI-first verification.
- **BOX_ACCESS_TOKEN** (optional) — For direct REST verification when Box CLI is unavailable.

## Quick Verification

For MCP requests, discover the tools exposed by the connection and call `who_am_i` to verify the intended Box user. Verify mutations through that same connection; unavailable capabilities require diagnosis or a permitted alternative workflow.

For explicit CLI requests, use Box CLI. For other Box requests, prefer MCP, then CLI, then direct REST as a last-resort fallback. The following examples verify CLI or REST access:

```bash
# With Box CLI installed and authenticated:
box users:get me --json
box folders:items 0 --json --max-items 5

# Last-resort fallback (for sessions where MCP/CLI are unavailable):
export BOX_ACCESS_TOKEN="your-token"
curl -sS -H "Authorization: Bearer $BOX_ACCESS_TOKEN" -H "Accept: application/json" "https://api.box.com/2.0/folders/0"
```

## Contributing

Skills follow the [Agent Skills specification](https://agentskills.io). Each skill is a directory with a `SKILL.md` file containing YAML frontmatter and markdown instructions, plus only the supporting resources its workflow requires.

## License

MIT

## Privacy and Support

- [Box Privacy Policy](https://www.box.com/legal/privacypolicy)
- [Box Support](https://support.box.com/)
