---
name: webcon-app-build
description: Build a WEBCON application, process or workflow (steps, paths, form fields, business rules, automations) through the DesignerStudio MCP server - design, build, verify, publish. Use when the user asks to create or extend a WEBCON application, process or workflow with the DesignerStudio MCP tools. Not for reading WEBCON data (webcon-mcp-assistant), design without a build (webcon-app-design) or environment setup (webcon-env-setup).
argument-hint: "[strict | balanced[=N] | full]"
---

# Build a WEBCON application through the DesignerStudio MCP

Part A tells how to work. Part B tells what are the constraints and limitations of the server. Part B goes stale: compare it with the live tool list at each build.

- At the start, before the first write:
  - Read the [environment files reference](../../references/environment-files.md) for file locations and formats. Ask the user if the files are somewhere else. Do not guess an environment value.
  - Environment type and default mode: the row of this connector in `webcon-environment/environments.md`. No row, no DesignerStudio tools, or auth errors: stop and tell the user to run `/webcon-designer-studio-mcp-skills:webcon-env-setup` first. It connects and registers the connector.
  - Name the DesignerStudio connector and its environment row. Do not change the connector or environment without the user.
  - Read `mcp-notes.md` for server-specific facts. Record new facts in the format in the reference.
- Names, modelling and diagram layout: use `design-defaults.md`. If it does not exist, ask the user for the language of names and use business names; suggest `/webcon-designer-studio-mcp-skills:webcon-env-setup`.
- Use the [example application descriptions](examples/README.md) as a model when the user asks for an example description or a test build.

# Part A. Guidance

## A1. Plan before the first write

1. Build from an approved plan. No plan: use the `webcon-app-design` skill first. A full description from the user (as examples 01-04) is the design: do only steps 4-8 of `webcon-app-design` (environment facts, capability check, manual tasks, build groups, approval), then build.
2. Extending an application: read what you extend with `get_*` first. Read the default field visibility of each workflow you add steps to.
3. Before the build, give the user the list of manual tasks (one line per object, with its exact value) and the open decisions. B2 traps have tool workarounds: follow them, they are not manual tasks. The user confirms the scope. A partial build (parts of the requirements not in the design) needs the explicit consent of the user before the first write and again before the publish.
4. Build groups: one group per process or workflow, at most about 40 write calls each, so that one subagent finishes a group in one context. Each group has a scope line (`designSessionId`, process, workflow, docType and workflow step form GUIDs), its full build table, the groups it needs GUIDs from, the groups it can run in parallel with (one data model = one writer at a time), and its read-back method. Put the publish blockers (B1) in the last group, or leave them to the user as manual tasks. Keep the plan, `guids.md` and the build logs in one build folder (ask the user; default `webcon-builds/<application name>/`).

## A2. Design session

- `get_current_design_session` gives the session for all calls. It is the draft of the user in Designer Studio: one per user, shared with the user, and a publish sends ALL its pending changes.
- Start of the build: no pending changes of other work (empty `changes` in `get_current_design_session`) and an empty `get_validation_state`. Else ask the user to publish or discard the other work first.
- Checkpoint at the end of each group: `get_validation_state` → fix what a tool can fix → run it again → M = open manual tasks. M = 0: publish (type `prod` or unknown: ask first). Publish at each clean checkpoint: a reset of the session removes all drafts, a published group stays.
- An entry is from this build only if its GUID or its `ownerId` is in `guids.md`. Record each `created.ruleGuid` there too. Other entries are foreign (not made by this build): a manual task for the user, never a fix.
- Other clients (Designer Studio, other MCP clients) can write to the same session during the build. Read `get_current_design_session` again right before each publish; if a change is not from this build, ask the user first.
- Only your own `get_validation_state` is a verdict. A tool `ok` or a subagent report is not. A shorter list with no change from you is not a fix.
- To find the change that made an entry: `undo_change` N times, then `redo_change` N times, with no new change between. `undo_all_changes` cannot be reversed and removes the changes of the user too.
- Probes (unknown keys or values): only when the session has no pending build work. Undo them after. A bad probe can break deletes for the whole session.

## A3. GUIDs and read-back

- The server makes the GUIDs and returns them in `created`. Never make a GUID or type one from memory: copy it from `guids.md` or from a tool result. Map results by name, not by return order. Write `guids.md` (object → GUID) once per group.
- `ok` does not prove a store: read back the draft with `get_*` (B2 lists the tool per object). `browse_*` can return the published state.
- Read back in small batches (about 10 calls), so that one failed read does not hide the others. If a read tool fails for one object, use another read of the same data (for example `get_step_form` or `browse_process_data_model` for a field) and do not repeat the failed call.

## A4. Cost

- Cost ≈ turns × context. Never one MCP call per turn in a loop.
- Subagents: one per group (A1.4). A cheaper model for work from a table. The brief has the goal, the scope line, the build table and the Part B lines of its area. No design decisions, no publish, no validation reads. Report: GUID table and exact errors only.
- Read a tool schema directly: load it once with tool discovery. Do not send a subagent to find a schema.
- Orchestrator: read only the plan section of the current group.

## A5. Manual tasks and stops

- Mode: `strict` stops at the first manual task, `balanced=N` (default 10) stops when M > N, `full` builds to the end. With an approved plan and M ≤ limit, do not stop between groups.
- One line per manual task: verb, object name as in the Designer Studio UI, full GUID, exact value, and the `get_designer_studio_link` result if the tool gives one (if not, continue without the link). If the `webcon-manual-tasks` skill is available, use its format.
- A clean `get_validation_state` shows only that the server found no errors. It does not show that the build is complete.
- End report: a table of each design element → built (read back) / manual task / not done. Do not call the build complete while a row is "not done". Then lines for `mcp-notes.md` and the manual tasks.

# Part B. Constraints and known limitations

Checked against WEBCON version 2027.1.1

## B1. Publish blockers

The MCP cannot complete these, and each blocks the publish of the whole session: empty rules, Choose fields without a data source, actions that need kind-specific configuration (`Missing…` entries).

## B2. Traps

- Read-back tools: `get_path` for path settings, `get_step_form` for the field matrix, `get_form_field` for field settings.
- A field is on its form type when it is created: do not associate it again. Associate only to put it on another form type.
- A new step in an existing workflow takes the default field visibility of the workflow. Set the restrictions for ALL fields, including item list columns and system fields.
- `set_step_form_field_restrictions`: send `ReadOnly` first: parallel calls on the same fields overwrite each other. `isRequired: true` also sets `isVisible: true`.
- `browse_steps` returns the published AI agent configuration: read the draft with `get_step`. Its `order` can be stale after a move.
- `browse_business_rules` lists only named rules, not the rules owned by steps, paths, fields or actions.
- `edit_path_config_task_assignment` `PredefinedAssignmentMode`: only 3-9.
