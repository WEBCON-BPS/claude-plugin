# `webcon-ai-prompt-builder` plugin (placeholder)

Not ported yet. Per [BT-US/7059](https://portal.webcon.com/db/2/app/30/element/226591/form)
this skill (source: `skills/webcon-ai-prompt-builder` in the `a-baszak/agents`
repo) is planned for the marketplace, alongside `webcon-mcp-assistant` and
`webcon-ai-agent-builder`.

To finish this plugin, mirror the `webcon-mcp-assistant` plugin next to it:

1. Copy the skill into `skills/webcon-ai-prompt-builder/`.
2. Add `.claude-plugin/plugin.json` (see the sibling plugin for the shape).
3. Add an `.mcp.json` only if the skill needs its own MCP server; otherwise omit it.
4. **Verify license and terms of use before publishing** — required by the ticket,
   not yet done for this skill.
5. Register the plugin in `../../.claude-plugin/marketplace.json` (bump its version
   out of `0.0.0` placeholder state).
