# WEBCON Claude Code Marketplace

A [Claude Code](https://code.claude.com/docs) plugin marketplace with skills for
working with **WEBCON** — reading workflow data through the WEBCON MCP
server and authoring AI-agent instructions and prompts for WEBCON automation.

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
| `webcon-mcp-assistant` | Read-only assistant for the General WEBCON MCP server — find tasks, track workflow instances, review attachments, and search documents and records. | Available (v0.2.0) |
| `webcon-ai-agent-builder` | Create and improve step-level instructions for WEBCON AI agents (agent behavior for workflow automation). | Available (v0.1.0) |
| `webcon-ai-prompt-builder` | Build effective prompts for the WEBCON "AI Prompt" business rule. | Coming soon |

Each plugin has its own README under [`plugins/`](plugins/) with full details.

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

On enable, Claude Code prompts you for two values (no environment variables to
pre-set). The token is masked and stored in your operating system's secure
credential store — **macOS Keychain**, **Windows Credential Manager**, or the
**Linux Secret Service / keyring**:

- **WEBCON MCP server URL** — on-prem `https://portal.yourcompany.com/mcp`, or SaaS `https://<tenant>.webcon.cloud/mcp`
- **WEBCON MCP token** — the bearer token / API key issued for that server

To change these later, reopen the plugin's configuration in `/plugin`.

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

Licensed under proprietary WEBCON terms — see [`LICENSE`](LICENSE). Use is also
subject to WEBCON's [EULA](https://webcon.com/eula/),
[Master Subscription Agreement](https://webcon.com/master-subscription-agreement/),
and [Privacy Policy](https://webcon.com/privacy-policy/).
