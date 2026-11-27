# Invocation

Paste this whole file as one message.

/webcon-designer-studio-mcp-skills:webcon-app-build balanced

# Setup

Build a new application "Purchase requests" in a new application group "Demo apps". Make the current user the application administrator. Assign all tasks to the current user, unless a rule below says something else. Do not change other applications. Names are in English.

# Processes

| Process | Acronym | Workflows |
|---|---|---|
| Purchase requests | PRQ | Purchase request |

# Steps

| Step | Type | Actor |
|---|---|---|
| Request registration | Start | Author |
| Superior approval | Standard | Superior |
| Value check | Branch | System |
| Financial approval | Standard | Current user |
| Request accepted | Positive end | - |
| Request rejected | Negative end | - |

# Paths

| Path | From | To |
|---|---|---|
| Register | Request registration | Superior approval |
| Approve | Superior approval | Value check |
| Reject | Superior approval | Request rejected |
| Return to author | Superior approval | Request registration |
| Small amount | Value check | Request accepted |
| Large amount | Value check | Financial approval |
| Approve | Financial approval | Request accepted |
| Reject | Financial approval | Request rejected |

# Form fields

| Field | Type | Note |
|---|---|---|
| Request name | Single line of text | Required on Register |
| Description | Multiple lines of text | |
| Requesting person | Person or group | |
| Superior | Person or group | Required on Register |
| Request date | Date | No time |
| Request value | Floating-point number | EUR, required on Register |

Item list "Request items":

| Column | Type | Note |
|---|---|---|
| Item name | Single line of text | Required |
| Price | Floating-point number | EUR |
| Quantity | Floating-point number | 0 decimal places |
| Total | Calculated column | Price × Quantity, show the sum |

Field visibility: all fields are editable on Request registration and read-only on all other steps.

# Rules

- Value check: Request value < 1000 EUR → path Small amount, else → path Large amount.
- Superior approval: assign the task to the person in the field Superior.
- Default values: Requesting person = author, Request date = today.

# Accepted manual tasks

I accept a partial build. Leave these items to me as manual tasks, and build all other items:

- Body of the Value check rule.
- Default values of Requesting person and Request date.
- Task assignment on Superior approval to the field Superior.
