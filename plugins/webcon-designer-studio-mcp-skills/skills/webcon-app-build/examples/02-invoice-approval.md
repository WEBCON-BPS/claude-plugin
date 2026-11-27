# Invocation

Paste this whole file as one message.

/webcon-designer-studio-mcp-skills:webcon-app-build balanced=3

# Setup

Build a new application "Invoice approval" in a new application group "Demo apps". Make the current user the application administrator. Assign all tasks to the current user, unless a rule below says something else. Do not change other applications. Names are in English.

# Processes

| Process | Acronym | Workflows | Purpose |
|---|---|---|---|
| Supplier invoices | SINV | Supplier invoice | Description and approval of cost invoices |
| Cost centers | CCTR | Cost center | Dictionary of cost centers and their approvers |

# Supplier invoices

## Steps

| Step | Type | Actor |
|---|---|---|
| Invoice registration | Start | Author |
| Describe invoice | AI agent | AI agent |
| Approval | Standard | Approver |
| Approved | Positive end | - |
| Rejected | Negative end | - |

## Paths

| Path | From | To |
|---|---|---|
| Register | Invoice registration | Describe invoice |
| Described | Describe invoice | Approval |
| Approve | Approval | Approved |
| Reject | Approval | Rejected |
| Describe again | Approval | Describe invoice |

## Form fields

| Field | Type | Note |
|---|---|---|
| Invoice number | Single line of text | Required on Register |
| Supplier name | Single line of text | Required on Register |
| Invoice date | Date | |
| Net amount | Floating-point number | |
| Cost center | Choice field | Values from Cost centers |
| Approver | Person or group | |
| Cost description | Multiple lines of text | Filled by the AI agent |
| AI justification | Multiple lines of text | Filled by the AI agent, read-only for users |

## AI agent "Invoice description"

Instructions: Read the invoice attachment and the form fields. Write a short description of the cost (what was bought and why) in Cost description. Write the reason for this description in AI justification. Do not change other fields.

# Cost centers

One step "Active" (start step), no paths.

| Field | Type |
|---|---|
| Code | Single line of text |
| Name | Single line of text |
| Approver | Person or group |

# Rules

- Approver: on path Register, set Approver = Approver of the selected cost center.
- Approval: assign the task to the person in the field Approver.

# Accepted manual tasks

I accept a partial build. Leave these items to me as manual tasks, and build all other items:

- Data source on the Cost centers process (Code, Name, Approver).
- Data source of the field Cost center.
- Rule that copies Approver from the selected cost center.
