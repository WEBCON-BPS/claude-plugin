# `webcon-ai-prompt-builder`

Helps you **write effective prompts for the WEBCON "AI Prompt" business rule** —
the rule that sends a single query to an LLM and returns a single text value.

## What it does

Given a task described in plain language, the skill proposes **at least two
prompt variants**, each labelled with the strategy it uses and when that
strategy is the right choice:

- **Zero-Shot** — a direct instruction, for simple unambiguous tasks.
- **Few-Shot** — instruction plus `input -> expected output` examples, for
  classification and data extraction where the format matters.
- **Persona** — assigns the model a domain-expert role, for tasks needing
  specialist judgement.

It encodes the rule's actual constraints so the output works by construction:

- Dynamic values come from the form via `{WFCON:...}` or from other business
  rules via `{BRD:...}`, and every prompt ends with an explicit list of its
  inputs.
- Attachments cannot be embedded in the prompt text — the skill directs you to
  pass them through the rule's first parameter using `GET ATTACHMENTS`.
- The result is always **one text string**. When a task needs several values,
  the skill recommends either splitting it across multiple `AI PROMPT` rules or
  returning a parseable format (`;`-delimited for `GET AT INDEX`, or JSON for a
  JS form rule / `SQL COMMAND`).

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
