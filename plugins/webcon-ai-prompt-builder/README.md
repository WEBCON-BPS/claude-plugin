# `webcon-ai-prompt-builder`

Write instructions for WEBCON AI Prompt business rule.

## What it does

Given a task described in plain language, the skill proposes at least two
prompt variants, each labelled with the strategy it uses and when that
strategy is the right choice.

It order for the instructions to be more accurate, the created prompt may contain placeholders for variables/references (example: from the form - via `{WFCON:...}` - or from business rules - via `{BRD:...}`). Your task will be to replace them with actual variables in the Designer Studio editor.

## When Claude uses it

Claude invokes this skill automatically when you ask it to write or improve a
prompt for the WEBCON AI Prompt business rule — for example, "write a prompt
that picks the right cost account from the description and department" or "help
me classify whether this email is a complaint or an enquiry."

## Usage

1. Install the plugin from the marketplace:
   ```
   /plugin install webcon-ai-prompt-builder@webcon-marketplace
   ```
2. Describe the task, ideally naming the process, step, and the form fields the
   prompt should read.
3. Claude returns the variants in `text` code blocks, ready to paste into the
   AI Prompt business rule in WEBCON Designer Studio.

## Configuration

None. This is a pure-content skill (guidance only) — it does not call the
WEBCON MCP server, so there is no `.mcp.json` and no `userConfig` to fill in.
Install it and the skill is available immediately.

## Contents

- `.claude-plugin/plugin.json` — plugin manifest.
- `skills/webcon-ai-prompt-builder/SKILL.md` — the skill itself.

## License

See [`LICENSE`](../../LICENSE) at the root of this marketplace.
