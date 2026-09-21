# `webcon-mcp-assistant`

Guide for using WEBCON MCP Server tools through Claude - patterns and assistance for common tasks.

See the official WEBCON documentation for details on the MCP server, its tools,
and authentication: [MCP Servers (WEBCON docs)](https://docs.webcon.com/docs/2026R2/Studio/MCPServers).

## What it does

Skill activates automatically when you ask Claude about things in your WEBCON environment, such as tasks, instances and available applications. It can also help you navigate the Portal and find reports, dashbords and start buttons.

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

### Generating the connect command

Run **`/webcon-mcp-assistant:connect <server URL>`** and Claude reads the
server's own OAuth metadata, pins the exact scopes it declares, and prints a
ready-to-paste `claude mcp add-json` line for both bash and PowerShell. It also
offers a preflight check that validates the redirect URI, the granted scopes and
the offline access setting with a single request, before anyone signs in.

An administrator must first register the OAuth client in WEBCON (Authorization
Code + PKCE, the exact scopes above, and the MCP client's redirect URL). For the
end-to-end walkthrough — deriving the URL and scopes from the server `/docs`
link, registering the client, and wiring both Claude.ai and Claude Code — see
the bundled **[`webcon-mcp-setup`](skills/webcon-mcp-setup/SKILL.md)** skill and
the [WEBCON docs](https://docs.webcon.com/docs/2026R2/Studio/MCPServers#configuration-and-setup).

### Registering the API application in WEBCON (admin checklist)

Two settings are easy to miss when the administrator registers the API
application (**Admin Panel → Integrations → API → New API application**,
type **user context**, authentication **Authorization code**):
An *application context* application will not work and does not even show these
two settings, because it only supports the client credentials grant.


1. **Authorized redirect URIs** — WEBCON only accepts a redirect URL that is
   registered *exactly* here. Register the entry for each surface you use:

   | Surface | Redirect URI to register |
   |---|---|
   | Claude Code CLI / this plugin | `http://localhost:8123/callback` |
   | Claude.ai (web) / Claude Desktop connector | `https://claude.ai/api/mcp/auth_callback` |

   By default Claude Code picks a **random localhost port** for the OAuth
   callback, so the redirect URL would differ on every login and could never be
   pre-registered. That is why this plugin pins the port with
   `"callbackPort": 8123` in `.mcp.json`. If you add the server manually with
   `claude mcp add-json`, keep the same `"oauth": { "callbackPort": 8123 }`, or
   pick another free port and register `http://localhost:<port>/callback`
   instead — the port in WEBCON and in `callbackPort` must match.

2. **Authorization flows configuration → Allow offline access (issue Refresh
   Tokens)** — tick this checkbox (it sits right under **Authentication type:
   Authorization code**).
   Claude Code automatically appends the `offline_access` scope to the
   authorize request whenever the WEBCON authorization server advertises it in
   `/.well-known/openid-configuration` (which it does), so the login fails with
   an *invalid scope* error if the application is not allowed to issue refresh
   tokens. You do **not** need to add `offline_access` to the plugin's
   **WEBCON OAuth scopes** field — Claude adds it on its own; only the option on
   the application has to be enabled. As a bonus, refresh tokens let Claude
   renew the access token silently instead of re-opening the browser login.

**To change the values later**, open the `/plugin` interface, select this
plugin, and re-enter its configuration (or edit the non-sensitive values in
`settings.json`). No credentials are ever stored in this repository.

Because every value is supplied at enable time, the same plugin works unchanged
for on-prem and cloud SaaS installations — there is no per-customer build.

## Contents

- `.claude-plugin/plugin.json` — plugin manifest, including the `userConfig` prompt schema.
- `.mcp.json` — MCP server registration (driven by `${user_config.*}`, see above).
- `commands/connect.md` — the `/webcon-mcp-assistant:connect` command, which
  generates the `claude mcp add-json` line for a given server.
- `skills/webcon-mcp-assistant/` — the read-only assistant skill.
- `skills/webcon-mcp-setup/` — step-by-step guide for connecting and authenticating a WEBCON MCP server.

## License

See [`LICENSE`](../../LICENSE) at the root of this marketplace.
