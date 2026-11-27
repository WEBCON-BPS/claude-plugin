# Example application descriptions

Files 01-04 describe a full design, including processes, steps, paths, fields, rules, and accepted manual tasks. File 05 provides an idea, setup facts, and limits for the agent to use when designing the application. Use either format as a model for your own description. With a full description (01-04), the build does only steps 4-8 of `webcon-app-design` (environment facts, capability check, manual tasks, build groups, approval). An idea (05) or a requirements document needs the full design procedure.

To test a build, open one of the files below, adapt the application group and administrators to your environment, then paste the entire file in one message. Include the first line, which invokes the skill with a mode. All examples create a new application and use "Demo apps" as the example group.

Each example gives consent for the partial scope it describes before the first write. Explicitly approve any proposed scope beyond that consent. A partial build still requires explicit consent before publishing.

| File | Mode | What it tests | Manual tasks |
|---|---|---|---|
| [01-purchase-requests.md](01-purchase-requests.md) | balanced | Item list with calculated column, branch step | 3 |
| [02-invoice-approval.md](02-invoice-approval.md) | balanced=3 | AI agent step, second process, choice field | 3 |
| [03-nda-approval.md](03-nda-approval.md) | strict | Field matrix, requiredness per path, return paths | 2 |
| [04-meeting-actions.md](04-meeting-actions.md) | full | Two linked workflows, item list | 3 |
| [05-equipment-loans-idea.md](05-equipment-loans-idea.md) | balanced | Idea only: the agent designs the application | at most 5 |
