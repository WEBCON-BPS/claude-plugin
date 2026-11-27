# Invocation

Paste this whole file as one message.

/webcon-designer-studio-mcp-skills:webcon-app-build strict

# Setup

Build a new application "NDA approval" in a new application group "Demo apps". Make the current user the application administrator. Assign all tasks to the current user, unless a rule below says something else. Do not change other applications. Names are in English.

# Processes

| Process | Acronym | Workflows |
|---|---|---|
| Non-disclosure agreements | NDA | Non-disclosure agreement |

# Steps

| Step | Type | Actor |
|---|---|---|
| Draft | Start | Owner |
| Legal review | Standard | Legal department |
| Manager approval | Standard | Current user |
| To be signed | Standard | Owner |
| Signed | Positive end | - |
| Rejected | Negative end | - |

# Paths

| Path | From | To |
|---|---|---|
| Send for review | Draft | Legal review |
| Accept | Legal review | Manager approval |
| Send back to author | Legal review | Draft |
| Approve | Manager approval | To be signed |
| Send back to author | Manager approval | Draft |
| Reject | Manager approval | Rejected |
| Mark as signed | To be signed | Signed |

# Form fields

| Field | Type |
|---|---|
| Title | Single line of text |
| Counterparty name | Single line of text |
| Counterparty tax ID | Single line of text |
| NDA type | Choice field, values Mutual / One-way |
| Valid until | Date |
| Owner | Person or group |
| Legal comments | Multiple lines of text |
| Signature date | Date |

# Field matrix

All fields are read-only by default.

| Step | Editable | Required |
|---|---|---|
| Draft | Title, Counterparty name, Counterparty tax ID, NDA type, Valid until, Owner | Title, Counterparty name, NDA type on Send for review |
| Legal review | Legal comments | Legal comments on Send back to author |
| Manager approval | - | - |
| To be signed | Signature date | Signature date on Mark as signed |

# Rules

- Legal review: assign the task to the group "Legal department".

# Accepted manual tasks

I accept a partial build. Leave these items to me as manual tasks, and build all other items:

- Fixed values Mutual / One-way for the field NDA type.
- Task assignment on Legal review to the group "Legal department".
