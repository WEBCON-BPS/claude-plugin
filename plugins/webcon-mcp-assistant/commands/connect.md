---
description: Generate a ready-to-paste `claude mcp add-json` command for a WEBCON MCP server, with the endpoint and scopes read from the server itself.
argument-hint: "[server URL — e.g. https://mcp.webconbps.com/api/mcp/server/1 or its /docs link]"
allowed-tools: WebFetch, Bash
---

# Connect a WEBCON MCP server

Produce the exact `claude mcp add-json` command for the WEBCON MCP server the
user named, deriving every value from the server instead of guessing. Do **not**
edit any files and do **not** run `claude mcp add-json` yourself — the user runs
it, because it prompts for a client secret.

User input: `$ARGUMENTS`

If the input is empty, ask for the server's `/docs` link or MCP endpoint and stop
until they answer.

## Step 1 — Derive the addresses

From the input, work out:

- **MCP endpoint** — the input with a trailing `/docs` removed. Example:
  `https://mcp.webconbps.com/api/mcp/server/1`.
- **Origin** — scheme and host only, e.g. `https://mcp.webconbps.com`.
- **Resource path** — the endpoint's path, e.g. `/api/mcp/server/1`.
- **Server name** — `webcon-server-<N>` where `<N>` is the last path segment.
  If that segment is not a plain number, fall back to `webcon-mcp`.

## Step 2 — Read the exact scopes from the server

Fetch, in this order, and use the first that returns JSON:

1. `<origin>/.well-known/oauth-protected-resource<resource path>`
2. `<origin>/.well-known/oauth-protected-resource`

Take `scopes_supported` from the response verbatim. That list is the pinned
scope set. The per-resource document is narrower than the generic one, so prefer
it. Never invent scopes and never copy them from an example.

Then fetch `<origin>/.well-known/openid-configuration` and note two things:

- whether its `scopes_supported` contains `offline_access`
- whether `registration_endpoint` is present

If `offline_access` is advertised, Claude Code will append it to every authorize
request and the API application must be allowed to issue refresh tokens. Say so
explicitly in the output. Do **not** add `offline_access` to the pinned scopes.

If `registration_endpoint` is absent, dynamic client registration is impossible,
so a client ID is mandatory. This is the normal case for WEBCON.

If a metadata document cannot be fetched, say which one and carry on with the
scopes the user supplies.

## Step 3 — Emit the command

Print the command in a `bash` code block, substituting the derived values and
leaving the client ID as a visible placeholder when the user has not given one:

```bash
claude mcp add-json <server-name> '{"type":"http","url":"<endpoint>","oauth":{"clientId":"<CLIENT-ID>","callbackPort":8123,"scopes":"<scopes>"}}' --client-secret --scope user
```

Then print the PowerShell form in a separate block, because PowerShell mangles
the embedded quotes unless parsing is stopped:

```powershell
claude --% mcp add-json <server-name> {"type":"http","url":"<endpoint>","oauth":{"clientId":"<CLIENT-ID>","callbackPort":8123,"scopes":"<scopes>"}} --client-secret --scope user
```

Explain in one line each that `--client-secret` prompts with masked input and
stores the secret in the OS credential store, and that `--scope user` makes the
server available in every project.

Never ask the user to paste the client secret into the conversation.

## Step 4 — State the admin prerequisites

List what must already be true in WEBCON, because the login fails otherwise:

- The API application is **user context** with authentication **Authorization
  code**. An application-context application only supports client credentials
  and does not even show the next two settings.
- **Authorized redirect URIs** contains `http://localhost:8123/callback`. Add
  `https://claude.ai/api/mcp/auth_callback` too if the same client will serve
  the Claude.ai or Claude Desktop connector.
- **Allow offline access (issue Refresh Tokens)** is ticked, when the
  authorization server advertises `offline_access`.
- **Application permissions (scopes)** grants every scope from Step 2.

## Step 5 — Offer the preflight check

Offer to verify the configuration before the user signs in. The check needs no
password and takes a second: it sends one authorize request and reads the
redirect. Run it only if the user agrees and they have supplied a client ID.

```bash
curl -s -o /dev/null -w '%{redirect_url}\n' "<origin>/connect/authorize?response_type=code&client_id=<CLIENT-ID>&code_challenge=E9Melhoa2OwvFrEMTJguCHaoeK1t8URWbuGJSstw-cM&code_challenge_method=S256&state=preflight&redirect_uri=http%3A%2F%2Flocalhost%3A8123%2Fcallback&scope=<url-encoded scopes>"
```

Read the result for the user:

| Redirect target | Meaning |
|---|---|
| a login page | configuration is correct, they can sign in |
| `errorDescription=Invalid%20redirect_uri` | `http://localhost:8123/callback` is not registered on this client |
| `errorDescription=Invalid%20scope%20for%20client` | at least one requested scope is not granted; re-run scope by scope to find it |
| `errorDescription=Unknown%20client%20or%20client%20not%20enabled` | wrong or disabled client ID |

If the scopes pass but the same request with ` offline_access` appended fails,
the offline access checkbox is unticked. That is the one Claude Code appends on
its own, so the sign-in will fail until it is enabled.

## Step 6 — Finish

Tell the user to run the generated command, then sign in with `/mcp` in an
interactive `claude` session, picking the server by the name you derived. Mention
that port 8123 must be free, and that `claude mcp logout <server-name>` clears
cached credentials if they change the client afterwards.
