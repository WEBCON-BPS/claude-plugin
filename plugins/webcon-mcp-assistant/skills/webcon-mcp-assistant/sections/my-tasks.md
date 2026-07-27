# GetMyTasks

- By default, do **not** send `statusFilter` at all.
- Add `statusFilter` only when the user explicitly asks for status-based filtering (new, expired, flagged, postponed, etc.).
- Leave `applicationIds` and `processIds` empty unless the request clearly requires narrowing.
- If app or process IDs are needed and unknown, use `GetAllSearchFilters` first.

## Results handling

- Treat `contextInformation` as process-specific hints; normalize by process or application, avoid cross-process assumptions, and show only fields present for each task.
- If no tasks are returned, state that only current assignee tasks appear and suggest contacting a process admin when tasks are expected.
