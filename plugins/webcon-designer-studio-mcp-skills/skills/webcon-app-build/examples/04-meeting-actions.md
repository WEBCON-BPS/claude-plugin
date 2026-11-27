# Invocation

Paste this whole file as one message.

/webcon-designer-studio-mcp-skills:webcon-app-build full

# Setup

Build a new application "Meeting actions" in a new application group "Demo apps". Make the current user the application administrator. Assign all tasks to the current user, unless a rule below says something else. Do not change other applications. Names are in English.

# Processes

| Process | Acronym | Workflows | Purpose |
|---|---|---|---|
| Meetings | MTG | Meeting | Plan a meeting and record minutes |
| Action items | ACT | Action item | Follow-up tasks from meetings |

# Meetings

## Steps

| Step | Type | Actor |
|---|---|---|
| Plan meeting | Start | Organizer |
| Planned | Standard | Organizer |
| Minutes | Standard | Organizer |
| Held | Positive end | - |
| Cancelled | Negative end | - |

## Paths

| Path | From | To |
|---|---|---|
| Plan | Plan meeting | Planned |
| Start minutes | Planned | Minutes |
| Cancel | Planned | Cancelled |
| Close | Minutes | Held |

## Form fields

| Field | Type | Note |
|---|---|---|
| Subject | Single line of text | Required on Plan |
| Meeting type | Choice field | Values Call / Online / On-site |
| Start | Date and time | Required on Plan |
| Organizer | Person or group | |
| Agenda | Multiple lines of text | |
| Notes | Multiple lines of text | Editable on Minutes |
| Follow-up needed | Yes/No choice | Editable on Minutes |

Item list "Next steps" (editable on Minutes):

| Column | Type |
|---|---|
| Action | Single line of text |
| Owner | Person or group |
| Due date | Date |

# Action items

## Steps

| Step | Type | Actor |
|---|---|---|
| New | Start | System |
| To do | Standard | Owner |
| Done | Positive end | - |
| Cancelled | Negative end | - |

## Paths

| Path | From | To |
|---|---|---|
| Create | New | To do |
| Complete | To do | Done |
| Cancel | To do | Cancelled |

## Form fields

| Field | Type | Note |
|---|---|---|
| Subject | Single line of text | |
| Owner | Person or group | |
| Due date | Date | |
| Related meeting | Single line of text | Signature of the source meeting |
| Result | Multiple lines of text | Required on Complete |

# Links between workflows

- On path Close (Minutes → Held), start one Action item for each row of Next steps. Mapping: Action → Subject, Owner → Owner, Due date → Due date, meeting signature → Related meeting. The new Action item goes on path Create.
- To do: assign the task to the person in the field Owner.

# Accepted manual tasks

I accept a partial build. Build to the end and leave these items to me as manual tasks:

- Fixed values Call / Online / On-site for the field Meeting type.
- Start of Action items from path Close, one per Next steps row, with the field mapping above.
- Task assignment on To do to the field Owner.
