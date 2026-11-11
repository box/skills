# Gemini Plugin Metadata

This directory contains Gemini-specific plugin configuration.

- `plugin.json` - Gemini plugin manifest (name, version, description, and other metadata)

Shared skills live at the repo root in `skills/` and are discovered with the plugin. The portable Box MCP endpoint is declared in the root `mcp.json`; authentication remains managed by the Gemini client.

For the plugin format, see the [Gemini plugin docs](https://antigravity.google/docs/build-plugins/).
