# Extractor brief: discover configuration patterns of a WEBCON environment

Goal: read existing applications through the DesignerStudio MCP and record the naming and design patterns that the maintainers of the environment use. The output is raw evidence; another agent aggregates it.

# Rules

- Read only. Call only `browse_*` and `get_*` DesignerStudio tools. Do not call `create_*`, `edit_*`, `delete_*`, `publish_*`, `undo_*`, `redo_*`, `set_*`, `reorder_*`, `associate_*`, `unassociate_*`, `change_*`, `move_*`, `add_*`, `remove_*`.
- Pass `designSessionId: null` (published state). If the schema of a tool does not accept null, do not call that tool; record it as a gap. Do not call `get_current_design_session`.
- Before the first call of a tool, load its live schema. Load all schemas you need in one call if the client allows it.
- Before the first call of a tool, read the lines for that tool in `mcp-notes.md` (path given by the caller).
- Budget: per application, all processes at process level; at most 2 workflows per process (prefer the main one); all steps of those workflows; `get_path` on at most 12 paths per workflow (mix of forward, back or reject, and system or hidden paths); `get_form_field` on at most 10 fields per process; `get_business_rule`, `get_action`, `get_automation` on at most 8 items each per process.
- If a read fails twice, record the tool and the error as a gap and continue with the next item.
- A successful call or a missing field does not prove a value. Record only what the read returns. Business rule bodies, action configuration, and automation blocks are often not readable: record names, documentation, return types, action kinds, and counts; do not guess the logic.

# What to record

Record literal names. Do not normalize, translate, or fix typos.

1. Application: name, description present?, icon, color, language, group, stage.
2. Process: name, acronym, description present?, process type, `allowOnBehalfSubstitutions`, `useTaskDelegation`. Document types (`browse_doc_types`): name, acronym, signature format.
3. Workflow: name, description present?, number of steps.
4. Steps: name, type, x, y, order, description present?. Compute: count by type; number of negative end steps; are x and y multiples of 24? of 8?; typical dx and dy between consecutive steps; is the layout diagonal, a horizontal row, a vertical column, or actor rows (same y for the steps of the same actor)?
5. Paths (`browse_paths_for_step`, then `get_path` on a sample): name, source → target, hidden or visible, system-style name (prefix in brackets or parentheses), comment required?, form validation off?, wait for tasks?, task-finishing mode, button style or color if returned, port positions and indexes. Classify each sampled path as forward, back (target earlier), reject (target negative end), or system.
6. Form fields (`browse_process_data_model`, `browse_form_fields_by_type`, `get_form_field` on a sample; if `get_form_field` fails, `get_step_form`): name, type, documentation present and its first words, name style (Title case, sentence case, camelCase, prefix codes), group and tab names, item list column names. Count fields by type.
7. Business rules (`browse_business_rules` per process, and global if the schema allows it): name, return type, documentation first words, name prefix pattern (Get …, Is … ?, and the same in other languages).
8. Automations and actions (`browse_automations`, `browse_global_automation_definitions`, `get_action` on a sample): automation names, type (path, step, global, template), action names and kinds, documentation first words, name prefix pattern. Action templates if visible.
9. AI agent steps: step name, instruction structure (headings), tags (`{WFCON:}`, `{PH:}`, `{BRD:}`), position against human steps.
10. Anything else that looks like a deliberate team decision: consistent prefixes, numbering, bilingual names, descriptions always filled, colors.

# Defaults and decisions

Mark a value as `default?` when Designer Studio probably sets it on a new object (for example the steps "Start" and "Finish (positive)", paths named after the target, empty descriptions, standard flags). Do not present such values as team decisions.

# Output

Write one markdown file at the path given by the caller:

```markdown
# <application names>
tools-failed: <tool: error> (one line each)

## Raw inventory
<per process and workflow: tables of steps, sampled paths, sampled fields, rules, automations and actions; literal names>

## Observed patterns
| # | Area | Pattern (as a maintainer would write the rule) | Evidence n/m | Examples (literal) | Default? | Confidence |

## Contradictions and one-offs
```

Return to the caller only: the file path, the counts of items read, and the 5 strongest patterns, one line each.
