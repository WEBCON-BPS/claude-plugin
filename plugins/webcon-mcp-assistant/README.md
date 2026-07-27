# `webcon-mcp-assistant`

A read-only assistant for the General **WEBCON MCP server** — find your tasks,
track workflow instances, review attachments, and search documents and business
records, straight from Claude.

See the official WEBCON documentation for details on the MCP server, its tools,
and authentication: [MCP Servers (WEBCON docs)](https://docs.webcon.com/docs/2026R2/Studio/MCPServers).

## Configuration (on-prem vs. SaaS)

WEBCON customers run either an **on-prem** installation or the **cloud SaaS**
edition, so the MCP server address is never fixed. Instead of hard-coding a
URL (or asking installers to pre-set environment variables), this plugin uses
Claude Code's [`userConfig`](https://code.claude.com/docs/en/plugins-reference#user-configuration)
mechanism: **when you enable the plugin, Claude Code prompts you** for the
values below and substitutes them into `.mcp.json` via `${user_config.*}`.

| Field | `userConfig` key | Meaning | Example |
|---|---|---|---|
| WEBCON MCP server URL | `mcp_url` | MCP endpoint (the `/docs` link with `/docs` stripped) | `https://mcp.webconbps.com/api/mcp/server/1` |
| WEBCON OAuth client ID | `mcp_client_id` | Client ID of the pre-registered OAuth client | issued per customer/tenant |
| WEBCON OAuth client secret | `mcp_client_secret` (`sensitive`) | Client secret of that OAuth client | issued per customer/tenant |
| WEBCON OAuth scopes | `mcp_scopes` | Exact space-separated scopes registered for the client | `Mcp.Tools openid profile email User.Elements.Read.All User.Tasks.Read.All` |

All are `required`. `mcp_client_secret` is `sensitive`, so it is masked on input
and stored in the OS keychain (not `settings.json`); the non-sensitive `mcp_url`,
`mcp_client_id`, and `mcp_scopes` are stored in `settings.json` under
`pluginConfigs[<plugin>].options`.

### Authentication

WEBCON MCP servers use **OAuth 2.1 (Authorization Code + PKCE) in user context**
with a **pre-registered** OAuth client — there is **no dynamic client
registration**, so the Client ID, Client Secret, and pinned scopes must be
supplied. On first connection Claude Code opens a browser login; every MCP
operation then runs as the signed-in user, respecting that user's WEBCON roles
and privileges.

An administrator must first register the OAuth client in WEBCON (Authorization
Code + PKCE, the exact scopes above, and the MCP client's redirect URL). For the
end-to-end walkthrough — deriving the URL and scopes from the server `/docs`
link, registering the client, and wiring both Claude.ai and Claude Code — see
the bundled **[`webcon-mcp-setup`](skills/webcon-mcp-setup/SKILL.md)** skill and
the [WEBCON docs](https://docs.webcon.com/docs/2026R2/Studio/MCPServers#configuration-and-setup).

> **Verified.** This OAuth flow is confirmed working end to end with the Claude
> Code CLI, the MCP Inspector, and LiteLLM MCP clients.

**To change the values later**, open the `/plugin` interface, select this
plugin, and re-enter its configuration (or edit the non-sensitive values in
`settings.json`). No credentials are ever stored in this repository.

Because every value is supplied at enable time, the same plugin works unchanged
for on-prem and cloud SaaS installations — there is no per-customer build.

## Contents

- `.claude-plugin/plugin.json` — plugin manifest, including the `userConfig` prompt schema.
- `.mcp.json` — MCP server registration (driven by `${user_config.*}`, see above).
- `skills/webcon-mcp-assistant/` — the read-only assistant skill.
- `skills/webcon-mcp-setup/` — step-by-step guide for connecting and authenticating a WEBCON MCP server.

## License

See [`LICENSE`](../../LICENSE) at the root of this marketplace.
