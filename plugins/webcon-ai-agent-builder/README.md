# `webcon-ai-agent-builder`

Helps you write instructions for WEBCON AI Agent.

## What it does

Given a business process, workflow, and target step, the skill produces a
clear, Markdown-formatted agent instruction (goal + numbered steps) that a
WEBCON AI agent can execute. It works in two modes:

1. **Write a new agent instruction** - Explain what the agent should do and get a well-structured, step-by-step instruction ready to use in Designer Studio.
2. **Improve an existing instruction** - Provide: current instructions; reasoning from last execution; description of the expected result. As a result, get a refined, more robust instructions.

It order for the instructions to be more accurate, the created prompt may contain placeholders for variables/references (example: from the form - via `{WFCON:...}` - or from business rules - via `{BRD:...}` - or from a transition path - via `{PH:...}`). Your task will be to replace them with actual variables in the Designer Studio editor.

## When Claude uses it

Claude invokes this skill automatically when you ask it to create or improve
WEBCON AI agent instructions — for example, "write an agent instruction to
classify incoming tickets and route them" or "improve this step instruction so
it fills the invoice fields from the attached PDF."

## Usage

1. Install the plugin from the marketplace:
   ```
   /plugin install webcon-ai-agent-builder@webcon-marketplace
   ```
2. Ask Claude to build or refine an agent instruction. If the process,
   workflow, or target step is missing, the skill asks for it before
   generating.
3. Claude returns the instruction in an `md` code block, ready to paste into
   the agent step in WEBCON Designer Studio.

## Configuration

None. This is a pure-content skill (guidance only) — it does not call the
WEBCON MCP server, so there is no `.mcp.json` and no `userConfig` to fill in.
Install it and the skill is available immediately.

## Contents

- `.claude-plugin/plugin.json` — plugin manifest.
- `skills/webcon-ai-agent-builder/SKILL.md` — the skill itself.

## License

See [`LICENSE`](../../LICENSE) at the root of this marketplace.
