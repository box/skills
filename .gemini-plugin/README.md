# Gemini Plugin Metadata

This directory contains Gemini-specific plugin configuration.

- `plugin.json` - Gemini plugin manifest (name, version, description, skills, MCP configuration, and other metadata)

Shared skills live at the repo root in `skills/`; the manifest lists each one in its `skills` array. The manifest's `mcpServers` field points to the root `mcp.json`, which declares the portable Box MCP endpoint; authentication remains managed by the Gemini client.

For the plugin format, see the [Gemini plugin docs](https://antigravity.google/docs/build-plugins/).
