---
description: Generate a ready-to-paste `claude mcp add-json` command for a WEBCON MCP server, with the endpoint and scopes read from the server itself.
argument-hint: "[server URL — e.g. https://<your-webcon-host>/api/mcp/server/<N>, or its /docs link]"
allowed-tools: WebFetch, Bash
---

# Connect a WEBCON MCP server

Produce the exact `claude mcp add-json` command for the WEBCON MCP server the
user named, deriving every value from the server instead of guessing, and verify
the OAuth client before they run it. Do **not** edit any files and do **not** run
`claude mcp add-json` yourself — the user runs it, because it prompts for a
client secret.

Every WEBCON installation has its own host, so placeholders below such as
`<your-webcon-host>` and `<N>` stand for whatever the user gave you. Never carry
a host, a server number or a scope from this file into your answer, and never
assume a default server: everything comes from the user's input and from their
own server's metadata.

Answer in the language the user wrote in.

User input: `$ARGUMENTS`

## Step 0 — Read everything the user already gave you

The goal is a command the user can paste without editing anything, so mine the
input for every value before asking for it. The input may be a bare URL, or a URL
with more values alongside it, in any order and any language.

- **Server URL** — the first `http(s)://…` that contains `/api/mcp/server/`, or
  failing that the only URL present.
- **Client ID** — a GUID, that is 8-4-4-4-12 hex digits. WEBCON client IDs always
  look like this, so a GUID anywhere in the input is the client ID.
- **Server name** — only if the user clearly names one, e.g. "call it webcon-prod".
- **Scope** of the registration — `--scope project` only if the user asks for the
  server to live in one repository, otherwise `--scope user`.

Then ask, in **one** message, only for what is genuinely missing:

- If the server URL is missing, ask for the `/docs` link or the MCP endpoint.
- If the client ID is missing, ask for it, and say the WEBCON administrator issues
  it when registering the API application.

Stop and wait for the answer. Never ask for the client secret, in this step or any
other: `--client-secret` prompts for it in the user's own terminal with masked
input, and it must never appear in the conversation.

Once the user answers, re-read their reply for the same values and continue. Only
if they explicitly decline to give the client ID, skip Step 2 and leave
`<CLIENT-ID>` in the output as a visible placeholder, saying plainly that the
command is incomplete until they paste it in.

## Step 1 — Derive the addresses and the scopes

From the user's URL, work out:

- **MCP endpoint** — their URL with a trailing `/docs` removed, of the shape
  `https://<their-host>/api/mcp/server/<N>`.
- **Origin** — scheme and host only.
- **Resource path** — the endpoint's path.
- **Server name** — `webcon-server-<N>` where `<N>` is the last path segment.
  If that segment is not a plain number, fall back to `webcon-mcp`.

Then fetch, in this order, and use the first that returns JSON:

1. `<origin>/.well-known/oauth-protected-resource<resource path>`
2. `<origin>/.well-known/oauth-protected-resource`

Take `scopes_supported` from the response verbatim. That list is the pinned scope
set. The per-resource document is narrower than the generic one, so prefer it.
Never invent scopes and never copy them from an example or from another server.

Then fetch `<origin>/.well-known/openid-configuration` and note two things:

- whether its `scopes_supported` contains `offline_access`
- whether `registration_endpoint` is present

If `offline_access` is advertised, Claude Code will append it to every authorize
request and the API application must be allowed to issue refresh tokens. Do
**not** add `offline_access` to the pinned scopes; Claude Code adds it itself.

If `registration_endpoint` is absent, dynamic client registration is impossible,
so a client ID is mandatory. This is the normal case for WEBCON.

Metadata often contains template scopes with angle brackets, such as
`User.Elements.Read.<ProcGuid>`. Those are server data. Leave them out of the
pinned set unless the user names a concrete one.

If a metadata document cannot be fetched, say which one and carry on with the
scopes the user supplies.

## Step 2 — Verify the client before the user commits to it

Do this **before** emitting the command, so the user never pastes a setup that
cannot sign in. It needs no password and takes a second: one authorize request,
and the redirect it returns is the answer. Tell the user you are checking, run the
request yourself with their origin, client ID and pinned scopes, and report the
verdict. Do not print the request itself unless they ask for it.

```
<origin>/connect/authorize?response_type=code&client_id=<CLIENT-ID>&code_challenge=E9Melhoa2OwvFrEMTJguCHaoeK1t8URWbuGJSstw-cM&code_challenge_method=S256&state=preflight&redirect_uri=http%3A%2F%2Flocalhost%3A8123%2Fcallback&scope=<url-encoded scopes>
```

Follow no redirects and read the `Location` header.

| Redirect target | Meaning |
|---|---|
| a login page | the client is ready, the user can sign in |
| `errorDescription=Invalid%20redirect_uri` | `http://localhost:8123/callback` is not registered on this client |
| `errorDescription=Invalid%20scope%20for%20client` | at least one requested scope is not granted to this client |
| `errorDescription=Unknown%20client%20or%20client%20not%20enabled` | wrong or disabled client ID |

**If the scopes are rejected**, find the offender by repeating the request one
scope at a time, but always keep `openid` in the request alongside the scope
under test. A scope tested alone produces a misleading answer: `profile` and
`email` alone return `Identity scopes requested, but openid scope is missing`,
and `offline_access` alone returns a bare `Invalid scope`. Neither means the
scope is ungranted. Only `Invalid scope for client` while paired with `openid`
does.

Report which scope is missing and ask the admin to grant it under **Application
permissions (scopes)**. Keep the full pinned set in the command anyway: the
server declares it needs that scope, so dropping it only moves the failure from
sign-in to the first tool call.

**If the scopes pass**, repeat the request once with ` offline_access` appended.
If that one fails while the first passed, the offline access checkbox is
unticked. Claude Code appends that scope on its own, so sign-in will fail until
it is enabled.

## Step 3 — Emit the command

Print the command with **every** value already filled in: the server name, the
endpoint, the client ID from Step 0 and the scopes from Step 1. Before sending,
re-read the command line itself and confirm it carries no angle brackets and no
value copied from this file. The only placeholder allowed to survive is the
client ID, and only when the user refused to supply it.

Lead with the form that matches the user's own shell and show the other one
second. On Windows the user is usually on PowerShell.

```bash
claude mcp add-json <server-name> '{"type":"http","url":"<endpoint>","oauth":{"clientId":"<CLIENT-ID>","callbackPort":8123,"scopes":"<scopes>"}}' --client-secret --scope user
```

PowerShell mangles the embedded quotes unless parsing is stopped, so it needs the
`--%` form:

```powershell
claude --% mcp add-json <server-name> {"type":"http","url":"<endpoint>","oauth":{"clientId":"<CLIENT-ID>","callbackPort":8123,"scopes":"<scopes>"}} --client-secret --scope user
```

Explain in one line each that `--client-secret` prompts with masked input and
stores the secret in the OS credential store, and that `--scope user` makes the
server available in every project.

Never ask the user to paste the client secret into the conversation.

## Step 4 — State the admin prerequisites

List what must be true in WEBCON. Mark the ones Step 2 proved, so the user does
not re-check them, and say plainly when a point was only inferred rather than
measured:

- The API application is **user context** with authentication **Authorization
  code**. An application-context application only supports client credentials
  and does not even show the next two settings. Reaching a login page in Step 2
  implies this, but does not prove it on its own.
- **Authorized redirect URIs** contains `http://localhost:8123/callback`. Step 2
  proves this directly. Add `https://claude.ai/api/mcp/auth_callback` too if the
  same client will serve the Claude.ai or Claude Desktop connector.
- **Allow offline access (issue Refresh Tokens)** is ticked, when the
  authorization server advertises `offline_access`. Step 2 proves this directly.
- **Application permissions (scopes)** grants every scope from Step 1. Step 2
  proves this directly.

## Step 5 — Finish

Tell the user to run the generated command, then sign in with `/mcp` in an
interactive `claude` session, picking the server by the name you derived. Mention
that port 8123 must be free, and that `claude mcp logout <server-name>` clears
cached credentials if they change the client afterwards.
