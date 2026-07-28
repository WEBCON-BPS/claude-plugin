---
name: webcon-mcp-assistant
description: 'Use this skill whenever a user asks anything related to WEBCON - finding tasks, tracking workflow instances, reviewing attachments, searching for documents or business records, or checking related items. Always use this skill for WEBCON read operations, even if the request seems simple (e.g., "what are my tasks?" or "find invoice X").'
compatibility: To be used in MCP-compatible AI clients with access to the General WEBCON MCP server. Some environments may also expose additional, administrator-added WEBCON MCP servers with write/automation tools.
---

# WEBCON MCP Assistant

## WEBCON Platform

WEBCON is a business process management platform for structured document workflows. Key concepts:

- **Application / Process** - organisational containers grouping related workflows (e.g. "Invoices", "Delegations").
- **Workflow instance** - a single case or record moving through a process (users call these "items", "cases", or "requests"). Each has a human-readable **instance number**, a current **step**, and a **form**.
- **Step** - the stage in the workflow where an instance awaits action.
- **Transition path** - a possible route to the next step.
- **Form** - fields and sections holding the instance's data.
- **Form fields (sometimes referred to as "attributes")** — a single value in the form.
- **Item list (sub-elements)** - a table embedded in a form.
- **Attachments** - files added to an instance.

## Output contract

- Briefly state what tool path was used.
- For search, say how `keywords` and `filters` were used and whether a retry was needed.
- Use a table for multiple found elements with columns:
  - `#` (instance number, hyperlinked to the element when a URL is available),
  - `Title`
  - `Step`
  - `Details` (most important form fields for a given element).
- In user-facing responses, identify elements by instance number and include a direct WEBCON link when available. Avoid leading with numeric element IDs, because they are less meaningful for end users.
- Include the direct element link for confirmed hits.
- For content search (`GetSearchResult`): always include `searchQueryUrl` in response, regardless of hit count - it lets user continue search in WEBCON portal directly.

## Load by intent

- Assigned work / my tasks filtering: [sections/my-tasks.md](./sections/my-tasks.md)
- Content search in workflow instances: [sections/search-content.md](./sections/search-content.md)
- Find report/view/dashboard/process by name, navigation-first shortcut and disambiguation: [sections/search-navigation.md](sections/search-navigation.md) (./load [sections/search-content.md](sections/search-content.md) only if navigation match is ambiguous/empty and you must run content search)
- Apps/menu/favorites/frequent places behavior: [sections/navigation-tools.md](./sections/navigation-tools.md)
- Proactive orientation for vague asks: [sections/proactive-orientation.md](./sections/proactive-orientation.md)
- Empty/inconclusive handling: [sections/error-and-empty-results.md](./sections/error-and-empty-results.md)
- Large result set summarization: [sections/large-list-defaults.md](./sections/large-list-defaults.md)
- Write/edit/transition/automation requests, or anything outside General MCP server's read-only scope: [sections/unsupported-actions.md](./sections/unsupported-actions.md)
