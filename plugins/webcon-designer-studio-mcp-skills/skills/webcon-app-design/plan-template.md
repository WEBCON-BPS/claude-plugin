# Plan template

The plan has two parts. Analysis describes the application: it follows the order of a WEBCON implementation analysis. Build tells `webcon-app-build` how to build it. The section names extend the format of the `webcon-app-build` examples (Setup, Processes, Steps, Paths, Form fields, Links between workflows, Accepted manual tasks): a full description from the user fills the same sections.

Keep all of the plan in one Markdown file: the workflows, the form fields and the field matrix are tables, not attached files. Leave out a section that has no content, except Scope decisions, Accepted manual tasks and Build groups. Placeholders are in `<angle brackets>`.

The template asks what to decide. The conventions in `design-defaults.md` give how. Do not write a convention into the plan; write only deviations.

Not in this template: presentation layer (reports, views, start page, dashboards), translations, data migration and initial import. If the source asks for them, write them in Scope decisions as out of scope, with the partial-build consent. A manual task for them does not replace the consent.

# Header

- Sources: `<files, sections, versions>`.
- Environment: `<connector name>` (row in `environments.md`), mode `<strict | balanced=N | full>`.
- Design defaults: `design-defaults.md`, as of `<date of the file>`. Proposal sections used where `design-defaults.md` has no section: `<names>`.
- Status: `draft` or `approved <date>`.

# Analysis

## 1. Application overview

### Goal

Two to four sentences: what the application does, for whom, what changes for the users.

### Scope decisions

Numbered assumptions. Each one is a decision about scope: in scope, out of scope, simplification, limit. When parts of the source are not in the design, write here the consent sentence for the user to confirm, as a quotation, for example: "I accept a partial build without `<source items>`."

### Changes from previous version or current state

Only for a new version of a plan or a change of an existing application.

| Item | Before | After | Reason |
|---|---|---|---|

### Setup

| Item | Value |
|---|---|
| Application | `<name>` (new / existing) |
| Application group | `<name and GUID, or open decision>` |
| Administrators, supervisor | `<logins>` |
| Language of names | `<from design-defaults.md>` |
| Applications that must not change | `<names>` |

## 2. Processes

| # | Process | Acronym | Workflows and form types | Kind (standard, helper, dictionary) | Purpose | Source |
|---|---|---|---|---|---|---|

## 3. Workflows

One subsection per workflow. The Steps and Paths tables are the workflow diagram.

### 3.n Workflow `<name>`

#### Steps

| Step | Type (start, standard, flow control, system, positive end, negative end) | Actor | Description | Source |
|---|---|---|---|---|

#### Paths

| From | Path | To | Kind (forward, back, negative, system) | Task assignment | Comment or validation | Rule | Automation |
|---|---|---|---|---|---|---|---|

- System paths and their visibility on the form.
- Flow-control steps: condition → path.

### Business rules

| Rule | Return type | Used in | Logic | Body buildable (yes / manual task) |
|---|---|---|---|---|

### Automations and links between workflows

Plan a new automation for each case that needs more than one simple action: an integration (web service call, data exchange), a complex validation, or a generated file. The automation handles the whole case; the path or step only calls it.

| Trigger (step, path) | Automation | Case (integration, validation, file, link, other) | Actions in order (kind) | Target process, workflow, path | Field mapping | Condition |
|---|---|---|---|---|---|---|

### Deadlines and timers

| Milestone | Deadline in the source | WEBCON item (date field, task deadline, reminder, outside WEBCON) |
|---|---|---|

## 4. Form fields

One subsection per process. Shared fields once, with the column "Used in".

| Group | Field | Control type | Used in (workflows) | Data source or fixed values | Default value | Note | Source |
|---|---|---|---|---|---|---|---|

- Item lists: one table per item list with its columns. For a read-only item list: the columns and the row source.
- Count per control type, compared with the attribute limits.
- Attachment categories.
- Form layout per form type: tab, column, order of fields and groups.

### Field matrix

One table per workflow.

| Field | `<step 1>` | `<step 2>` | … |
|---|---|---|---|

Values: read-only, editable, required, hidden. Include item list columns and system fields. Mark the cells that the user must confirm.

## 5. Privileges and groups

| Role | Name in the source | Group | Task assignment (People field, group, rule, author) |
|---|---|---|---|

- Groups to create, with their members or the source of the members.
- Privileges: application, process, workflow, form type, and record level (for example by a field value).
- Write each item that no tool can build as a manual task in section 11.

## 6. Integration

### 6.1 Data sources

| Data source | Kind (WEBCON process, SQL, fixed values, external) | Used by (fields, item lists, rules) | GUID if it exists | Filter |
|---|---|---|---|---|

### 6.2 Web services

| Service | Direction (in, out) | Trigger (step, path, timer) | Data sent and received | Authentication |
|---|---|---|---|---|

## 7. E-mail notifications

| Trigger (step, path, timer) | Kind (task notification, custom e-mail) | Recipients | Subject | Content |
|---|---|---|---|---|

## 8. Printouts (optional)

| Printout | Format (PDF, Word, Excel) | Template | Trigger (step, path, by hand) | Data |
|---|---|---|---|---|

## 9. Source items not covered

Items of the source that the design does not cover and that need a decision or a workshop.

## 10. Open decisions

| # | Decision | Default | Status |
|---|---|---|---|

# Build

## 11. Accepted manual tasks

One line per object: object name as in the Designer Studio UI, what to do, exact value. Total M, compared with the limit of the mode. Planned probes (unknown keys) with the group that runs them and what happens to the probe objects.

## 12. Build groups

| Group | Scope line | Contents | Write calls (estimate) | Needs GUIDs from | Parallel with | Blocking checks | Read-back |
|---|---|---|---|---|---|---|---|

Scope line: names of the process, workflow and form type. The build fills the GUIDs from `guids.md` and tool results; the plan never gives a GUID that does not exist yet. Publish blockers in the last group, or as manual tasks.

## 13. Build notes

- Per process: start step, end step and name of the automatic path that the create tool makes; extra end steps.
- Diagram positions per `design-defaults.md`.
- Expected validation entries per object (from Part B of `webcon-app-build` and `mcp-notes.md`), so that the build can tell expected entries from new ones.

## 14. Deviations from design defaults

| Convention | Deviation | Reason |
|---|---|---|

## 15. Build session instructions

- Do not read the sources again: this plan holds the analysis and the decisions.
- Ask only for a missing environment value.
- Read Part B of `webcon-app-build` and `mcp-notes.md` first. Run with the mode in the header.

# Pre-build checklist

Before you show the plan for approval, check that the plan gives:

- For each choice field: the data source (section 6.1). A choice field with fixed values and no data source is a publish blocker (B1 of `webcon-app-build`): a manual task, or the last build group.
- For each read-only item list: the columns and the row source.
- The default value of each field that has one.
- For each system path: hidden or visible on the form.
- The attachment categories.
- For each path that creates a task: the assignment (field, group, rule or author).
- For each group and privilege: built or manual task.
- For each StartWorkFlow action: the company GUID, target process, workflow, path and field mapping.
- For each web service and e-mail notification: built or manual task, with its exact values.
- For each integration, complex validation and generated file: its own automation, with its actions in order.
- The attribute count per control type, compared with the limits.
- The form layout: tab, column, order. A group keeps the same order on all form types.
- The field specification in one table in the plan, not in other files.
- The field matrix per step, written out.
- The dependencies and the parallel groups of the build.
- For each probe: the group that runs it, and keep or remove of its objects.
- The call estimate and the read-back method per group.
- The manual tasks: one line per object, not per category.
