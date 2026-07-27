---
name: webcon-mcp-setup
description: >
  Hand-holding guide for connecting a WEBCON MCP server to Claude. Use when the
  user wants to add, install, configure, or authenticate a WEBCON MCP server (a
  "https://.../api/mcp/server/N" URL), or when WEBCON tools are missing/return
  auth errors. Covers BOTH surfaces: Claude.ai / Desktop custom connectors AND
  Claude Code CLI (`claude mcp add`). Handles pre-registered OAuth clients
  (no dynamic client registration).
---

# WEBCON MCP — Setup Guide

You are guiding a user (and/or their WEBCON admin) through connecting a **WEBCON
MCP server** to Claude. WEBCON MCP servers are per-application/per-server remote
HTTP MCP servers protected by OAuth 2.1 (Authorization Code + PKCE) with a
**pre-registered** OAuth client — there is **no Dynamic Client Registration**, so
the admin must register a client in WEBCON first.

Work through the phases below **in order**. Do not skip the admin registration
phase — the CLI/connector step will fail with a redirect-mismatch or
"Incompatible auth server: does not support dynamic client registration" error if
the client is not registered correctly first.

---

## Phase 0 — Collect the server link and derive everything from it

Ask the user for their server's **docs link**, e.g.:

```
https://mcp.webconbps.com/api/mcp/server/1/docs
```

From that one link, derive (use WebFetch to read the docs page and the metadata):

1. **MCP endpoint URL** = the docs link with `/docs` stripped:
   `https://mcp.webconbps.com/api/mcp/server/1`
2. **Server slug** for naming = `server-<N>` (here `server-1`).
3. **Required scopes** — read them from the docs page ("Required Scopes") AND/OR
   from `<origin>/.well-known/oauth-protected-resource`. Example for server 1:
   `Mcp.Tools User.Elements.Read.All openid profile email`
4. **Authorization server + resource** — confirm
   `<origin>/.well-known/oauth-protected-resource` returns
   `authorization_servers` and `resource`. If it does, **OAuth discovery is
   automatic** — you do NOT need to configure token/authorize endpoints.

> ⚠️ **Pin the exact scopes from THIS server's docs.** If scopes are not pinned,
> Claude falls back to `scopes_supported` from the metadata and requests *all*
> advertised scopes (e.g. `User.Designer.ReadWrite.All`), which the registered
> client may not be allowed to grant → the login fails. Always request exactly
> the scopes the admin registered.

Present the derived values back to the user and confirm before proceeding.

---

## Phase 1 — Admin registers the OAuth client in WEBCON (human step)

This must be done by someone with admin rights in WEBCON, **in the context of the
user** who will use the connection. Tell the admin to register an OAuth client
(API Key) with:

- **Grant type:** Authorization Code (+ PKCE)
- **Scopes:** exactly the ones derived in Phase 0
- **Redirect URI(s):** register **both** of the following so the same client works
  in both surfaces:

  | Surface | Redirect URI to register |
  |---|---|
  | Claude.ai (web) / Claude Desktop connector | `https://claude.ai/api/mcp/auth_callback` |
  | Claude Code CLI / plugin | `http://localhost:8123/callback` |

  > The CLI redirect is `http://localhost:<PORT>/callback`. This guide
  > standardizes on port **8123**. If 8123 is already used on the user's machine,
  > pick another free port and register `http://localhost:<that-port>/callback`
  > instead — the port here must match the `callbackPort` used in Phase 2B.

Registration output the user must copy:

- **Client ID**
- **Client Secret**

Never ask the user to paste the Client Secret into chat. In the CLI path it is
entered via a masked prompt; in the connector path it is entered into the Claude
settings UI.

---

## Phase 2 — Wire it into Claude

Pick the path(s) that match the user's surface. If they want both, do both — the
single registered client supports both.

### Phase 2A — Claude.ai (web) / Claude Desktop connector

1. Open **Settings → Connectors → Add custom connector**.
2. **Name:** `WEBCON <app/server name>` (e.g. `WEBCON Server 1`).
3. **Remote MCP server URL:** the MCP endpoint from Phase 0
   (`https://mcp.webconbps.com/api/mcp/server/1`) — **not** the `/docs` URL.
4. Under **Advanced settings**, provide the **OAuth Client ID** and **OAuth Client
   Secret** from Phase 1 (required because the server has no dynamic client
   registration).
5. Save, then click **Connect** and complete the browser login. The redirect goes
   to `https://claude.ai/api/mcp/auth_callback` (already registered in Phase 1).
6. Confirm the connector shows as connected and its tools are listed.

### Phase 2B — Claude Code CLI / plugin

Generate this command, filling in the derived URL and scopes, and have the user
paste their Client ID (the secret is prompted for by `--client-secret`):

```bash
claude mcp add-json webcon-server-1 \
  '{"type":"http","url":"https://mcp.webconbps.com/api/mcp/server/1","oauth":{"clientId":"<ClientId>","callbackPort":8123,"scopes":"Mcp.Tools User.Elements.Read.All openid profile email"}}' \
  --client-secret --scope user
```

Notes:

- `callbackPort` **must** match the localhost redirect registered in Phase 1 (8123).
- `--scope user` makes the server available in all of the user's projects. Use
  `--scope project` to commit it to a specific repo's `.mcp.json` instead.
- `--client-secret` prompts for the secret with masked input; it is stored in the
  OS keychain / credential store, **not** in `.mcp.json`. (For non-interactive
  setups, `MCP_CLIENT_SECRET=… claude mcp add-json …` also works.)

Then trigger login:

```
/mcp
```

Select `webcon-server-1` and complete the browser login (localhost redirect).
Re-login later with `claude mcp login webcon-server-1`; clear creds with
`claude mcp logout webcon-server-1`.

---

## Phase 3 — Verify

Confirm the connection works before declaring success:

1. In Claude Code, run `/mcp` and check the server is **connected** (not "needs
   auth" / "failed").
2. Call a lightweight read tool and confirm real data comes back — e.g.
   `GetApplications` or `GetCurrentUserData`.
3. If tools are missing, re-check that scopes were pinned and that the redirect
   URI registered in WEBCON exactly matches the surface used.

> **Tool routing must be prefix-agnostic.** The MCP tools appear as
> `mcp__<server>__GetMyTasks`, `mcp__<server>__GetApplications`, etc. The server
> segment varies (it may be a GUID or `webcon-server-1`). When routing to WEBCON
> tools, match on the **suffix** (`GetMyTasks`, `GetElement`, …), never on a
> hardcoded server name.

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| `Incompatible auth server: does not support dynamic client registration` | No client_id supplied (CLI) | Add `--client-id`/`clientId`; complete Phase 1 first |
| Login opens then fails with redirect mismatch | Wrong/missing redirect URI in WEBCON | Register the exact redirect for the surface (table in Phase 1); CLI port must equal `callbackPort` |
| Login succeeds but tools error / fewer scopes | Scopes not pinned, or client lacks a scope | Pin exact scopes from `/docs`; have admin grant them to the client |
| Metadata not discovered | `.well-known/oauth-protected-resource` unreachable | Set `oauth.authServerMetadataUrl` in the config as an override |
| `/docs` link given as the MCP URL | Trailing `/docs` not stripped | Use the base endpoint `.../api/mcp/server/N` |
