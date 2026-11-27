# Configuration

## Names

When writing, use full names of the configuration elements that exist in the Designer Studio (e.g. names of types of action or form fields) and in the configuration (full names of form fields, steps, paths etc.).

Be precise about what you describe.

| Bad | Better |
|---|---|
| When manager approves .. | On path "Approve" in "Manager approval" step |

- Always use keywords that are meaningful to the business,
- Do not use uncommon abbreviations,
- Do not use the element type in its name,
- Avoid articles and pronouns.
- Never use generic step names such as "Step 1", "Decision" or "Handling".
- Path names tell the action: "Send for approval", "Approve", "Return to author". Not "Next" or "OK".
- Give a return path the name of its target.

## Modelling

- Route every rejection to one negative end step, unless the process needs more.
- A step where a person decides is an intermediate step.
- Only automatic rule-based routing is a flow-control step. A flow-control step needs an outgoing path for each case.
- A step where the instance waits for the system, not for a person, is a system step. This is also true when it waits for a sub-workflow.
- Use one process with several workflows when the workflows share one data model, one history and one counter. Use it when the instances are the same business object in different variants.
- Use one process per workflow when the workflows are different business objects or need different data.
- Use a helper process for data that other processes choose from (a list that users maintain).
- Use a dictionary process type for a plain list that has no life cycle.

## Automations

Use when two or more actions need to collaborate (e.g., always occur in the same order). Even if this group of actions appears only in one configuration location.

Use one automation for each integration (web service call, data exchange), complex validation, or generated file. Name it after the case, not after its actions.

Use an automation instead of an action template, even for one action, if you must pass parameters that depend on the place where the action is used.

Ideally, global automations should be like black boxes; their operation should not depend on actions outside the specific automation. To send information to and from automations, use input and output parameters (in addition to retrieving values from form attributes). Process automations should follow this principle as well, but may be more flexible depending on the specific requirements.

Replace action execution conditions with the IF / SWITCH operators. IF operators can be nested within each other; 2-3 levels are acceptable, but more usually means that something can be done more simply (or broken down into smaller automations). If necessary, instead of further nesting IF conditions, configure the action execution condition.

## Action templates

Use. The more steps, paths and actions, the more useful they are.

Create them early and remember to write the documentation - if changed, it will have to be changed manually in every place where this action template is used.

Even if a template will be used once, it still can be used to group similar actions next to each other, for example:

- All "send a custom e-mail" actions - it's easier to change all e-mail templates at once.
- All "Add / remove privileges" actions

## Business rules

Use. The more actions and form fields you have, the more useful they are.

If the rule is simple (like comparing two values), it can be very significant from the business perspective (thus should be easily available to check, change and document).

If the rule is complex, you can give it a meaningful name and description so one doesn't have to think about the specifics when using this rule (just read the name and you know what it does - figuring it out by just looking at the 'code' will be hard).

If possible, rules should be independent of the place where they are used. Then the same rule can be used as an action execution condition, form field default value, path visibility etc. If it's important to use it in a specific place (like only on path transition), state it in the documentation.

Parameters - use when necessary, but don't sacrifice simplicity (readability) for a fancy configuration. Parameters can be used to allow a rule to be used in many places.

| What a rule does? | Name (starts with) | Documentation | Remarks |
|---|---|---|---|
| Returns data from a data source<br>Returns data from form fields (calculates values based on form fields) | **Get …** | **Returns …** | |
| Returns Boolean | **Is … ?** | **Returns true, if …** | |
| Returns a list of users | **Role - {role / actor name}** | **Returns …** | |
| Returns users for task assignment | **Task - {step name}** | **Returns …** | **Can use rules which return lists of users.** |

## Actions

| Action type | Name (starts with) | Documentation | Remarks |
|---|---|---|---|
| *All* | | **Executed if …** (if has an execution condition) | If the execution condition is a business rule, its details do not have to be described here but in that business rule documentation. Otherwise, write it in the documentation. |
| Change value of a single form field | **Set {form field name}** | **Sets {form field name}** | If it uses a business rule to set a value, then the same rule as above also applies here. |
| Create a task | **Task for {role name}** | **Creates a task for {role name}** | **As above** |
| Validate form | **If ..**<br>**Check …**<br>**Is …** | **Validates if …** | Documentation can be very similar to the error message displayed to the user. |
| Send a custom e-mail | **Notify {role name}** | **{Purpose of the notification}** - usually similar to action name<br>**To: {recipients…}**<br>**Subject: {e-mail subject}** | Recipients and content (subject, header, etc.) are not automatically generated in the documentation, thus they should be written manually. |

## Form fields

| Field type | Name (starts with) | Documentation | Remarks |
|---|---|---|---|
| *All types* | | **By default ….** (if default value is defined)<br>**Set automatically after selecting value in {name of a choice field}** (if the field depends on the choice field selection)<br>**Visible …** / **Editable …** / **Required …** (if has one of these conditions defined)<br>**Values: …** (fixed values of a choice field are listed in its documentation) | Add a description of any validations for this field (remember about consistency with the documentation of related business rules and actions dependent on this form field). |

## Form layout

- Use one tab for a simple form.
- Group the fields that one actor fills together.
- Put the identification fields first. Then put the groups in the order of the process.
- A group keeps the same order number on all form types of the process.
- Shared fields keep the same place on all form types.

## Field matrix

| Case | Rule |
|---|---|
| Default per step | Fields are read-only. |
| Step of the actor who fills a field | The field is editable. |
| Path where the value must exist | The field is required. Do not require it earlier. |
| Start step | The fields of the requester are editable. The fields that the first path needs are required on that path. |
| End steps | All fields are read-only. |
| Item lists and system fields | Set the item list columns and the system fields too. |
| Back paths | No field validation (see the Paths section). |

## Steps

### Documentation

- Each step has a description.
- For a step with an actor: write who the actor is and what the actor does or decides. Use one or two sentences.
- For a flow-control step: write the question and the rule that answers it.

## Paths

### Configuration

Set "wait for required tasks to be completed" (when necessary). By default, new paths set other tasks as cancelled (allowing anyone with modification privileges to go to the next step without ending tasks).

On negative paths (usually moving back to previous steps) - turn on comment requiredness and disable form fields validation.

When a task on the next step is assigned to the current user, enable wizard mode.

Validate with an action of type "Validate form", not with the additional validation of the path. An action leaves a trace in the history and is easier to debug. Use the form rules of the path only for optional confirmation messages and simple alerts.

### System paths

Name starts with a constant prefix, e.g.: (system) … (api) … (SAP API) …

Always hidden from the users.

Do not use paths used by users in the actions configuration. There is no limit to the number of paths on each step, so even one system path can be dedicated to one action (reuse paths between actions only if you have a good reason. Remember to document that then).

Disable form fields validation and cancel all tasks.

### Documentation

Documentation is optional for paths. Exception: write **Visible if …** when a path has a visibility condition.

For a system path, write where it is used: the action name and its process, workflow and step. Designer Studio has no usage report for paths.

### Paths on flow control steps

Path name should be an answer to the question from the name of the step and contain the name of the next step (it makes it easier to read the flow control configuration).

Example:

Step name: "Is contract value > 20k EUR?"

Paths: "Yes - to Approval", "No - to Archive"

(just from the names it's clear what is being checked and what are the next steps depending on the answer).

## Task assignment

| Case | Use |
|---|---|
| The person depends on the instance | A People form field |
| Any member of a team can do the task | A group |
| The person depends on other data | A rule named "Task - {step name}" |
| Return to the requester | The author |

Document the choice in the path or step documentation.

## Workflow diagram

- Each row is one actor. Steps of the same actor are in the same row.
- Use a diagonal layout, from top left to bottom right. The next step is about 216–264 to the right and 144–240 down. Put steps on a 24 grid (x and y are multiples of 24).
- The direction of a path comes from the process, not from the diagram. A path to a step that the instance already passed is "back", also when it goes down on the diagram.
- Forward path: leaves the source on the bottom (or right) side, enters the target on the left side.
- Back path: leaves the source on the top (or left) side, enters the target on the right side.
- The middle port slot of a side is for the main path.
- Put a negative end step that has paths from most steps at the bottom left. Put a negative end step with one incoming path next to its source step.
- Put steps that come after the final step (renewal, reopen) to the right of the end of the diagonal, in the row of their actor.
