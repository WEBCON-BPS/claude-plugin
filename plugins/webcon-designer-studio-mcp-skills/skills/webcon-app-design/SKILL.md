---
name: webcon-app-design
description: Design a WEBCON application, process or workflow and write its build plan for the DesignerStudio MCP - from requirements, a process map, an idea or a change request. Read-only use of the MCP. Use when the user asks to design, plan or scope a WEBCON application before a build, or gives requirements to turn into a plan. Not for the build itself (webcon-app-build) or environment setup (webcon-env-setup).
---

# Design a WEBCON application and write its build plan

Write one plan with the [plan template](plan-template.md). The plan must be complete: `webcon-app-build` builds from it without this skill and without the source. Use of the DesignerStudio MCP is `read-only`: only `browse_*` and `get_*` tools, never a write to the design session.

- At the start, for each input:
  - Environment files: read the [environment files reference](../../references/environment-files.md) for file locations and formats. Ask the user if the files are somewhere else.
  - Environment and mode: the row of the connector in `environments.md`. You need it for the plan header and the limit of `manual task` lines. No row: tell the user to run `/webcon-designer-studio-mcp-skills:webcon-env-setup` first, or continue without a connector and make the capability check an `open decision`.
  - Conventions: apply `design-defaults.md` to each design decision. If it does not exist, ask the user for the language of names, use business names, and suggest `/webcon-designer-studio-mcp-skills:webcon-env-setup`.
  - Server facts: read `mcp-notes.md` and Part B of the `webcon-app-build` skill. You need them for the capability check (step 5) and the build groups (step 7).
- Plan location: ask the user; default `webcon-builds/<application name>/plan-<date>.md`. The build keeps `guids.md` and the build logs in the same folder.

# Scale

| Input | Procedure |
|---|---|
| Full description (as `webcon-app-build` examples 01-04) | Short: steps 4-8. The description of the user gives the design sections. |
| Idea (as example 05) | Full: you design, the user approves. |
| Requirements document, process map, workshop notes | Full, with traces to the source, source items not covered and open decisions. |
| Change of an existing application | Read what you change with `get_*` first. The plan has only the delta and the section "Changes from current state". |

# Procedure

1. Read the source once.
2. Write the scope decisions as numbered assumptions. Each unclear item is an `open decision` with a default value. Ask the user only when no default is safe.
3. Design the Analysis part of the template. If a guide for the topic is in `guides/`, read it before you decide. Group the steps of a large workflow in phases. Write a `trace` (source section or map item) next to each design element. Walk each use case through the design: actor, step, visible fields, required fields. Each gap is an `open decision`. Done when: each source item has a `trace`, an `open decision` or a line in "Source items not covered", and each use case gives the actor, step, visible fields and required fields, or an `open decision` for the gap.
4. Collect the environment facts now, not during the build: application group, administrators, data source GUID of each choice field, company GUID and target workflow of each StartWorkFlow action, constants and global rules to reuse, applications that must not change, attribute limits per control type. If no read tool gives a value, ask the user, or make it an `open decision`. Changing an application: read the default field visibility of each workflow that gets new steps.
5. Capability check: for each design element, find the live tool or key that builds it. Compare with Part B of `webcon-app-build` and `mcp-notes.md`. Confirm unsure keys and action kinds with read tools (`browse_action_kinds`, `get_form_field`). No tool = a `manual task`.
6. Write the `manual task` lines in section 11 of the template. Count them (M) and compare M with the limit of the mode.
7. Split the build into groups by the rules in A1.4 of `webcon-app-build`. Give the scope line and the write call estimate of each group (section 12 of the template).
8. Check the plan against the pre-build checklist in the template. Show the user the scope decisions, the `open decision` list and the `manual task` lines. Set the plan status to `approved` only after the user accepts. If the user then asks for the build, continue with `webcon-app-build` and the `approved` plan.

# Gotchas

- Do not guess an environment value or a GUID.
- An unknown key that read tools cannot confirm is not a `manual task`: it becomes a blocking probe in the first build group.
- A publish blocker (B1 of `webcon-app-build`) goes in the last build group, or it is a `manual task`. It blocks the publish of the whole session. It is never a probe: a probe is only for an unknown key.
- A link between workflows that the MCP cannot configure is a `manual task` with its exact values. Never drop it.
- A partial build (parts of the source not in the design) needs the explicit consent of the user, written in Scope decisions.
- Do not write to `design-defaults.md` or `environments.md`. `webcon-env-setup` owns them.

# Rules

- Apply the conventions. If `design-defaults.md` has no section for a decision (for example form layout or field matrix), use the section of the [configuration conventions proposal](../webcon-env-setup/configuration-conventions-proposal.md) and name the proposal sections you used in the plan header (one line, not one open decision per section). Write a deviation from `design-defaults.md` only in the section "Deviations from design defaults", with its reason.
- Do not copy server facts into the plan as rules. The build reads `mcp-notes.md` itself. The plan gives only the expected validation entries and the manual tasks that follow from them.
- The plan does not tell the build when to stop or publish: the mode and the checkpoints of `webcon-app-build` decide this.
- If the `webcon-manual-tasks` skill is available, write the manual task lines in its format.
