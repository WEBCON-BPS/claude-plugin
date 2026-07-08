# `webcon-ai-agent-builder` plugin

Bundles the `webcon-ai-agent-builder` skill (copied verbatim from
`skills/webcon-ai-agent-builder` in the `a-baszak/agents` repo). The skill
helps users **write and refine the step-level instructions that drive WEBCON
AI agents** in workflow automation.

## What it does

Given a business process, workflow, and target step, the skill produces a
clear, Markdown-formatted agent instruction (goal + numbered steps) that a
WEBCON AI agent can execute. It works in two modes:

1. **Create new** — turn a described task into a well-formed instruction.
2. **Modify existing** — improve a current instruction based on the user's
   reasoning and expected behaviour.

It encodes WEBCON-specific conventions so the output is correct by
construction, including:

- Using only the actions a WEBCON agent actually has (set field, picker field,
  item-list row, choose path) and never inventing capabilities it lacks.
- Referencing form objects with system tags — `{WFCON:...}` (field),
  `{BRD:...}` (business rule), `{PH:...}` (path).
- The **pipeline, not loop** execution model: enforce every constraint inside
  the step that produces a value, never in a separate retry step.
- Pushing deterministic branching (thresholds, exact-match routing) out to
  standard WEBCON business rules instead of asking the LLM to do fixed logic.

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

- `.claude-plugin/plugin.json` — plugin manifest (version `0.1.0`).
- `skills/webcon-ai-agent-builder/SKILL.md` — the skill itself, unmodified from the source repo.

## License / publication note

Per [BT-US/7059](https://portal.webcon.com/db/2/app/30/element/226591/form),
this skill's license and terms of use **must be verified before any public
listing**. Do not publish this plugin to a public marketplace until that check
is done and recorded in [`../../docs/plan.md`](../../docs/plan.md).
