# Changelog

All notable changes to the Box Agent Skills (`box/skills`) are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project aims to follow [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- `box-cli` as an independently installable, single-file skill for verified Box CLI operations, automation, bulk recovery, and narrow REST fallback.

## [0.3.0] - 2026-06-30

Restructured the plugin into a layered architecture with a foundation skill that routes to focused reference files and workflow skills. MCP capability content (previously planned as separate Layer 2 skills) was flattened into reference files within the foundation skill for better retrieval — no separate install step, no cross-skill prerequisite chaining, and lower total context usage since references load on demand.

### Changed

- **Foundation (`box`)** — reframed from a monolithic skill into a foundation and routing skill (auth/setup, MCP vs. CLI vs. REST selection, rate limits, troubleshooting). MCP capability guidance lives in focused reference files loaded selectively via the domain routing table: `references/content-workflows.md`, `references/mcp-search.md`, `references/collaboration.md`, `references/ai-and-retrieval.md`, `references/mcp-hubs.md`, and `references/mcp-doc-gen.md`.
- **Legal workflow skills** — reworked into a leaner shared-foundation model that defers judgment calls to the firm and its attorneys. `box-legal-workflows` now holds shared building blocks; intake, contract, and M&A skills each declare explicit prerequisites and point to reference files for Box tool mechanics. (~1,000 → ~280 lines total)

Previous "Layer 2 Skills" were merged into the foundation skill as references.

### Added

- `references/collaboration.md` — new standalone reference for sharing, collaborator roles, shared links, and external-sharing rules (split from `content-workflows.md` + new MCP tool guidance).
- `references/mcp-search.md` — keyword, folder-name, and metadata search via MCP.
- `references/mcp-hubs.md` — Box Hubs creation, item management, and hub-level Q&A.
- `references/mcp-doc-gen.md` — Doc Gen template registration and document generation.

### Removed

- `examples/box-prompts.md` — worked into the existing references.

## [0.2.0] - 2026-05-05

Added the legal workflow skills on top of the foundation.

### Added

- `box-legal-workflows` — shared legal concepts referenced by the other legal skills (risk rating, human-in-the-loop requirements, confidentiality, Box AI governance, collaboration roles, and metadata strategy).
- `box-legal-workflows-intake` — client intake and onboarding automation.
- `box-legal-workflows-contract` — contract review and monitoring automation.
- `box-legal-workflows-ma` — M&A virtual data room setup and management.

## [0.1.0]

Initial public release of the Box Agent Skills plugin.

### Added

- `box` foundation skill covering Box integrations, content workflows, webhooks, and Box AI retrieval, with `references/` deep dives and example prompts.
- Platform plugin packaging for Codex, Cursor, and Claude Code.
