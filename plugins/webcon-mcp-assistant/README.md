# `webcon-mcp-assistant` plugin

Bundles the `webcon-mcp-assistant` skill (copied verbatim from
`skills/webcon-mcp-assistant` in the `a-baszak/agents` repo) with an
`.mcp.json` that points at a customer's General WEBCON MCP server.

## Configuration (on-prem vs. SaaS)

WEBCON customers run either an **on-prem** installation or the **cloud SaaS**
edition, so the MCP server address is never fixed. Instead of hard-coding a
URL (or asking installers to pre-set environment variables), this plugin uses
Claude Code's [`userConfig`](https://code.claude.com/docs/en/plugins-reference#user-configuration)
mechanism: **when you enable the plugin, Claude Code prompts you** for the two
values below and substitutes them into `.mcp.json` via `${user_config.*}`.

| Field | `userConfig` key | Meaning | Example |
|---|---|---|---|
| WEBCON MCP server URL | `mcp_url` | Base URL of your General WEBCON MCP server | `https://portal.customer.com/mcp` (on-prem) or `https://<tenant>.webcon.cloud/mcp` (SaaS) |
| WEBCON MCP token | `mcp_token` (`sensitive`) | Bearer token / API key for that server | issued per customer/tenant |

Both are marked `required`, so Claude Code will not let you finish with an
empty value. `mcp_token` is marked `sensitive`, so it is masked on input and
stored in the OS keychain (not `settings.json`). The non-sensitive `mcp_url`
is stored in `settings.json` under `pluginConfigs[<plugin>].options`.

**To change the values later**, open the `/plugin` interface, select this
plugin, and re-enter its configuration (or edit the non-sensitive `mcp_url`
in `settings.json`). No credentials are ever committed to this repo.

This is the answer to the "different server addresses per customer" constraint
recorded in [`../../docs/plan.md`](../../docs/plan.md) — no plugin code changes
per customer, only the values you provide at enable time.

## Contents

- `.claude-plugin/plugin.json` — plugin manifest, including the `userConfig` prompt schema.
- `.mcp.json` — MCP server registration (driven by `${user_config.*}`, see above).
- `skills/webcon-mcp-assistant/` — the skill itself, unmodified from the source repo.

## License / publication note

Per the ticket, this skill's license and terms of use **must be verified
before any public listing**. Do not publish this plugin to a public
marketplace until that check is done and recorded in `docs/plan.md`.
