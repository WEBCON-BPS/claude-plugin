# WEBCON Claude Code Marketplace

The official [Claude Code](https://code.claude.com/docs) plugin marketplace for
**WEBCON** — read workflow data through the WEBCON MCP server and author
AI-agent instructions for WEBCON automation, without leaving your editor.

## About WEBCON

WEBCON is a low-code Business Process Management platform for building and
running structured document workflows. Core concepts these plugins work with:

- **Application / Process** — containers grouping related workflows (e.g. "Invoices", "Delegations").
- **Workflow instance** — a single case/item/request moving through a process, with an instance number, a current step, and a form.
- **Step** — the stage where an instance awaits action; **transition paths** route it to the next step.
- **Form / Attribute (field)** — the data held on an instance; **item lists** are tables in a form and **attachments** are files on it.

Learn more at [webcon.com](https://webcon.com).

## Plugins

| Plugin | Description | Status |
|---|---|---|
| [`webcon-mcp-assistant`](plugins/webcon-mcp-assistant/) | Read-only assistant for the General WEBCON MCP server — find tasks, track workflow instances, review attachments, and search documents and records. | Available (v0.3.0) |
| [`webcon-ai-agent-builder`](plugins/webcon-ai-agent-builder/) | Create and improve step-level instructions for WEBCON AI agents (agent behavior for workflow automation). | Available (v0.1.0) |
| `webcon-ai-prompt-builder` | Build effective prompts for the WEBCON "AI Prompt" business rule. | Planned |

Each available plugin has its own README under [`plugins/`](plugins/) with full details.

## Use it with the Claude Code CLI

The commands below are **Claude Code slash commands** — type them inside an
active Claude Code session, not in your OS shell. They are identical on
**Windows, macOS, and Linux**; start Claude Code the usual way for your OS
(`claude` in PowerShell/Terminal/bash) and run them at the prompt.

### 1. Add the marketplace

```text
/plugin marketplace add WEBCON-BPS/claude-plugin
```

### 2. Install a plugin

```text
/plugin install webcon-mcp-assistant@webcon-marketplace
/plugin install webcon-ai-agent-builder@webcon-marketplace
```

### Alternative: install from your shell (non-interactive)

The same steps are available as `claude plugin` subcommands you run directly in
your OS shell (PowerShell, Terminal, or bash) — handy for scripts and CI. These
are identical on Windows, macOS, and Linux:

```bash
claude plugin marketplace add WEBCON-BPS/claude-plugin
claude plugin install webcon-mcp-assistant@webcon-marketplace
claude plugin install webcon-ai-agent-builder@webcon-marketplace
```

Add `--scope project` to share the install with everyone who clones a repo, or
`--scope local` to keep it gitignored (default is `user`).

### 3. Configure `webcon-mcp-assistant`

WEBCON MCP servers are protected by **OAuth 2.1 (Authorization Code + PKCE)**
against a **pre-registered** OAuth client — there is no dynamic client
registration, so your WEBCON administrator registers the client first and gives
you its credentials. On enable, Claude Code prompts you for four values (no
environment variables to pre-set):

- **WEBCON MCP server URL** — your server's `/docs` link with `/docs` stripped, e.g. `https://mcp.webconbps.com/api/mcp/server/1`
- **WEBCON OAuth client ID** — from the client your admin registered
- **WEBCON OAuth client secret** — masked on input and stored in your operating system's secure credential store (**macOS Keychain**, **Windows Credential Manager**, or the **Linux Secret Service / keyring**)
- **WEBCON OAuth scopes** — the exact space-separated scopes registered for that client, e.g. `Mcp.Tools openid profile email User.Elements.Read.All User.Tasks.Read.All`

On first use Claude Code opens a browser login; every MCP call then runs as the
signed-in user, respecting that user's WEBCON roles and privileges. To change
these later, reopen the plugin's configuration in `/plugin`.

The bundled `webcon-mcp-setup` skill walks you (and your admin) through the
whole thing — just ask Claude to *"set up my WEBCON MCP server"*.

`webcon-ai-agent-builder` needs no configuration — it is a pure-content skill.

## Example usage

Once installed, just ask Claude in natural language — it invokes the right
skill automatically:

**`webcon-mcp-assistant` — read WEBCON data**

```
What are my tasks?
Find the invoice from Contoso and show its current step.
Show the attachments on instance INV/2026/0042 and summarize them.
```

**`webcon-ai-agent-builder` — author agent instructions**

```
Write an agent instruction that classifies an incoming ticket and routes it to the right path.
Improve this step instruction so it extracts the vendor, amount, and due date from the attached PDF invoice.
```

## Company & license

Published by **WEBCON S.A.** ([webcon.com](https://webcon.com) · [contact](https://webcon.com/contact/)).

Released under the MIT License — see [`LICENSE`](LICENSE). Use of the WEBCON
platform itself remains subject to WEBCON's [EULA](https://webcon.com/eula/),
[Master Subscription Agreement](https://webcon.com/master-subscription-agreement/),
and [Privacy Policy](https://webcon.com/privacy-policy/).
