---
name: webcon-ai-agent-builder
description: Use this skill when the user wants to create or improve AI agent instructions for WEBCON - including writing new step-level instructions, modifying existing ones based on reasoning, or defining agent behavior for workflow automation.
---

# WEBCON AI Agent Builder

Helps users create or improve instructions for WEBCON AI agents. Two modes: (1) create new, (2) modify existing based on reasoning.

## WEBCON Platform

WEBCON is a business process management platform for structured document workflows. Key concepts:

- **Application / Process** — organisational containers grouping related workflows (e.g. "Invoices", "Delegations").
- **Workflow instance** — a single case or record moving through a process (users call these "items", "cases", or "requests"). Each has a human-readable **instance number**, a current **step**, and a **form**.
- **Step** — the stage in the workflow where an instance awaits action.
- **Transition path** — a possible route to the next step.
- **Form** — fields and sections holding the instance's data.
- **Form fields (sometimes referred to as "attributes")** — a single value in the form.
- **Item list (sub-elements)** — a table embedded in a form.
- **Attachments** — files added to an instance.
- **System tags** — references to platform objects: `{WFCON:ID}` (form field identifier), `{BRD:ID}` (business rule result), `{PH:ID}` (transition path).

## Context

If missing, ask before generating: business process name, workflow name, and target step.
If the user supplies current instructions + reasoning + expected behaviour → modification mode.

## Available agent actions

Base instructions only on these (don't name the tool; keywords suffice):

- **Path** - "go to next step / choose path / move forward".
- **Set field** - "fill in / enter / set".
- **Picker field** - "set value in picker field".
- **Item list** - "add row / update row".

Agent **cannot**: send email, fetch external data, generate files, or act outside WEBCON.

Field refs: always use `{WFCON:...}`. If the ID is unknown, use the field name as placeholder, e.g. `{WFCON:Description / Content}` (user resolves it in Designer Studio). Never optional ("if available"), never bare `XX`.

## Picker values

The agent receives a picker's available values at runtime — you don't need to list them. Describe the _criteria_ for choosing ("set {WFCON:Category} to the best-matching category"), not the inventory.

List values only when:

- the choice follows a rule, not a semantic match (e.g. "if amount ≥ 5000 → _Capital expense_, else _Operating expense_");
- labels are ambiguous or the agent should pick from a constrained subset;
- a weaker model picks poorly and needs the explicit candidates.

## Execution model: pipeline, not loop

Two levels of "loop", opposite implications:

- **Between steps (tool calls) - no loop.** Once a step completes, the agent never returns. A self-check as a _separate_ step fails: "step 2 write, step 3 verify & redo 2" → agent runs step 3 and moves on regardless. No backward branching; order is final.
- **Inside one step - reasoning is free.** Everything before the agent emits a value in a step is one generation: CoT, drafting, self-critique, correction all work. So put verification _inside_ the generating step:

```md
2. Write the summary. Identify target language from {579}, draft it, re-read,
   correct any word not in the target language, output only the final HTML.
```

Rule: enforce every constraint (language, format, length) inside the step that produces the value, never in a downstream retry step.

**Don't leak this vocabulary into output.** Pipeline/single-pass/CoT is guidance for you, not the agent (it's not self-aware). Never write "generate in one pass", "with in-step self-check", etc. Express the check as plain ordered steps (draft → check → correct → output); ordering alone does the work.

## Tips to pass to the user (when relevant)

- Complex multi-stage tasks (analysis + updates): consider splitting across several agents.
- Give details: field names, attachment types, conditions.
- Agent auto-receives all fields + values; no need to map them unless critical.
- Actions outside available tools: implement via standard WEBCON actions on the chosen path.
- Agent obeys Field Matrix permissions - ensure it can edit the target fields.

### Don't make the LLM do deterministic logic

If a step is a fixed rule over values the agent has already set (thresholds, exact-match branching, lookups), it is not a reasoning task — move it out of the instruction. The agent sets the fields; implement the branching as a standard WEBCON business rule on the transition path.

Example — instead of instructing the agent:
"if {WFCON:Severity} is Critical → {PH:Escalate}, else if {WFCON:Category} is Billing → {PH:Billing queue}, ..."
have the agent set {WFCON:Severity} and {WFCON:Category}, then let a business rule on the path choose the route.

Keep path selection in the agent only when the choice itself needs judgment the fields don't already capture.

## Rules for writing instructions

1. Optionally state the agent's role; always state the main goal.
2. Per action: which fields to set (`{WFCON:...}`), which attachments, which item-list rows.
3. Include logical conditions and the transition to the next step.
4. Clear keywords; system tags only to reference objects.
5. Agent auto-receives all fields/values - don't fetch them.
6. Pipeline: no separate retry/re-run steps; enforce constraints in the generating step.
7. Output: simple Markdown (`#`/`##` headings, numbered steps), returned in an `md` code block.

## Golden examples

Each shows the expected instruction style for a distinct action pattern. Mirror these.

### Email / ticket classification → path routing

```md
# Goal

Classify the incoming message.

## Steps

1. Read the message content and attachments.
2. Set {WFCON:Category} to one of: complaint, inquiry, order.
3. Choose path {PH:Classified}.
```

### Incoming mail categorization → routing by intent/urgency

```md
# Goal

Categorize the attached letter or email.

## Steps

1. Read the attached letter/email.
2. Set {WFCON:Topic} and {WFCON:Urgency} based on the content and sender intent.
3. Choose path {PH:Categorized}.
```

### Meeting notes summarization → summary + fields + in-step self-check

```md
# Goal

Process the transcript attachment and populate the summary fields, then move forward via {PH:149}.

## Steps

1. Read the transcript attachment.
2. Determine the target language from {579}.
3. Draft a concise internal summary in that language; re-read and correct any wording — including labels — not in the target language.
4. Set {WFCON:Internal summary} to the summary, {WFCON:Action items} to a bullet list of actions, and {WFCON:Deadline} to the earliest due date mentioned (or empty if none).
5. Choose path {PH:149}.
```

### Incoming invoice pre-processing → extract fields from a PDF

```md
# Goal

Extract invoice data from the attached PDF into the form fields.

## Steps

1. Read the attached invoice PDF.
2. Set {WFCON:Vendor}, {WFCON:Amount}, and {WFCON:Due date} from the document.
3. Leave any field empty if its value is not present in the document.
4. Choose path {PH:Submit}.
```

### Candidate CV screening → structured evaluation + numeric score

```md
# Goal

Read the attached CV and fill in the evaluation form.

## Steps

1. Read the attached CV.
2. Set {WFCON:Key competencies} to a short list of the candidate's main skills.
3. Set the picker field {WFCON:Seniority} to the best-matching level.
4. Set {WFCON:Match score} to a number from 0 to 100 reflecting fit to the role.
5. Choose path {PH:Reviewed}.
```
