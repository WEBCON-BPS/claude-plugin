---
name: webcon-env-discover
description: Read the published configuration of a WEBCON environment through the DesignerStudio MCP (read only) and report the configuration conventions that its maintainers already use, with evidence. The report is input to the webcon-env-setup conventions walkthrough, not adopted design defaults. Started from /webcon-env-setup (observe option), or run manually to refresh the report.
disable-model-invocation: true
---

# Discover the configuration conventions of a WEBCON environment

The result is a list of observed patterns with evidence. It is not a set of adopted design defaults: each pattern still needs the user's decision in the `webcon-env-setup` walkthrough. The sections of the report come from what the environment shows, not from the sections of the configuration conventions proposal.

Read the [environment files reference](../../references/environment-files.md) for file locations. If `webcon-env-setup` started this procedure, use its location and do not ask again. Else ask the user if they want to use another location before you look for or write the files.

Read a connector that has a row in `environments.md`. If it has none, stop and tell the user to run `/webcon-designer-studio-mcp-skills:webcon-env-setup` first.

## Rules

- Read only. Call only `browse_*` and `get_*` DesignerStudio tools. Do not call `get_current_design_session`.
- Read the published state: pass `designSessionId: null`. If the schema of a tool does not accept null, do not call that tool; record it as a gap.
- Before the first call of a tool, read its live schema and the lines for that tool in `mcp-notes.md`.
- Record literal names. Do not translate, normalize, or fix typos.
- A pattern needs evidence: count (n of m), 2–3 literal examples, the applications it comes from, and a confidence.
- Do not claim what the server cannot show. If a read fails twice, record a gap and continue.

## 1. Inventory

1. Call `browse_application_groups` (all groups) and `browse_applications` (`includeLegacy: true`).
2. Call `browse_processes` for each application. Record the name, acronym, process type, and flags.
3. Readability probe: call `browse_workflows` on one process of each application. If it fails, keep only the process-level data of that application and do not send an extractor to it.
4. Ask the user which applications to leave out of the evidence, and which untouched application (if any) can be the default baseline (see step 3).

## 2. Extract

Send one extractor per 1–3 applications. If subagents are available, run them in parallel; otherwise, do the extraction yourself, one group at a time. Give each extractor the [extractor brief](extractor-brief.md), its application GUIDs, and the path of its output file.

Budget per application: all processes; at most 2 workflows per process; all steps of those workflows; at most 12 `get_path` per workflow; at most 10 `get_form_field`, 8 business rules, and 8 actions per process.

| Area | Tools | What to record |
|---|---|---|
| Application | `browse_applications` | name language, description, icon, color, group |
| Process, document types | `browse_processes`, `browse_doc_types` | names, acronym style, signature format |
| Steps | `browse_steps`, `get_step` | names, types, count of negative end steps, x/y grid, layout shape |
| Paths | `browse_paths_for_step`, `get_path` | names by path class (forward, back, reject, system), comment required, validation, wait for tasks, hidden, ports, color |
| Form fields | `browse_process_data_model`, `get_form_field`, `get_step_form` | name style, prefixes, types, documentation, groups |
| Business rules | `browse_business_rules` (process and global) | name prefixes, return types, documentation |
| Automations, actions | `browse_automations`, `browse_global_automation_definitions`, `get_action` | names, kinds, templates, documentation |
| Path buttons | `browse_paths_for_step`, `get_path` | button names, button order |
| AI agent steps | `browse_steps` (agent type), `get_step` | step names, instruction structure, tags (`{WFCON:}`, `{PH:}`, `{BRD:}`), position against human steps |

If `get_form_field` fails, use `browse_process_data_model` or `get_step_form`.

One more extractor reads the global level (global business rules, global automations) and the default baseline application, if the user named one.

## 3. Aggregate

Default baseline: values that Designer Studio sets on a new object are defaults, not decisions. Use an untouched application that the user named in step 1 (for example one with only the steps "Start" and "Finish (positive)" and the path "Go to next step"); read `get_path` on its default path for the path flags. Do not create objects to get a baseline.

If there is no baseline application, treat these values as probable defaults and mark them "default, not verified":

- x and y on the 24 grid (Designer Studio snap); main path ports right/2 → left/2.
- Flags of a new path: comment not required, validation on, `IsDefault` true on the first path.
- Signature `{DTYPE_Acronym}/{YEAR}/{MONTH}/{NUMBERINMONTH:5}`; document type named like the workflow.
- Default names: "Start", "Finish (positive)", "Go to next step", `Automation on path "<name>"`, "New AI Agent", "New automation".
- `allowOnBehalfSubstitutions` true; the `INLINE_` prefix of inline automations.

Then:

1. Merge the extractor tables. For each pattern, count the applications that follow it, break it, or have no case.
2. Mark patterns equal to the default baseline as defaults.
3. Score each pattern: strong (most applications, two or more maintainers), weak (one application or one maintainer), conflict (applications disagree).
4. Map each pattern to a section of `design-defaults.md` if it exists, otherwise of the [configuration conventions proposal](../webcon-env-setup/configuration-conventions-proposal.md): agrees, contradicts, or new.

## 4. Report

Show the report to the user. After the user confirms it, write it to `observed-conventions.md`. If `webcon-env-setup` started this procedure, go back to its conventions walkthrough. Otherwise, tell the user to run `/webcon-designer-studio-mcp-skills:webcon-env-setup` and choose the observe option with the existing report.

```markdown
---
title: Observed conventions
asOf: YYYY-MM-DD
server: <MCP server name>
---

# Corpus
Applications read, applications left out, baseline, tools that failed.

## <area>
| Pattern | Evidence n/m | Examples | Applications | Default? | Confidence | Section: agrees / contradicts / new |

## Conflicts
## Gaps
What the MCP could not show.
```
