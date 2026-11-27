# Environment files

The default location is `webcon-environment/` in the working directory. If the team uses a different location, give it to all skills. Keep the files in a shared repository so everyone working on the same environment uses the same files.

| File | Written by | Read by |
|---|---|---|
| [`design-defaults.md`](#design-defaultsmd) | `webcon-env-setup` | `webcon-env-setup`, `webcon-app-design`, `webcon-app-build` |
| [`environments.md`](#environmentsmd) | `webcon-env-setup` | `webcon-env-setup`, `webcon-app-design`, `webcon-app-build` |
| [`observed-conventions.md`](#observed-conventionsmd) | `webcon-env-discover` (also run by `webcon-env-setup`) | `webcon-env-setup` |
| [`mcp-notes.md`](#mcp-notesmd) | `webcon-app-build` | `webcon-app-design`, `webcon-app-build`, `webcon-env-discover` |

## `design-defaults.md`

The team's adopted configuration conventions (names, modelling, workflow diagram, and documentation). One set applies to all environments. Start the file with:

```yaml
---
title: Configuration conventions
status: adopted
asOf: YYYY-MM-DD
namesLanguage: English
source: configuration-conventions-proposal.md
---
```

`asOf` is the date of the last walkthrough, and `namesLanguage` is the language of names in the configuration. `source` is the starting document: `configuration-conventions-proposal.md`, the file name of the user's document, or `design-defaults.md`; for the observe option, add `observed-conventions.md` (for example `design-defaults.md + observed-conventions.md`). Put `as of YYYY-MM-DD` on each section; update a section's date only when the team changes it or confirms it again. "Keep" in the walkthrough is a confirmation.

## `observed-conventions.md`

The conventions that the maintainers of an environment use, read from its published configuration, with evidence. These are observed patterns, not adopted rules: `webcon-env-setup` shows them during the walkthrough, and only the user's decisions go into `design-defaults.md`. The format is in the `webcon-env-discover` skill.

## `environments.md`

One row per MCP connector. Keep other connector rows when adding a row. Only `webcon-env-setup` adds rows, after it checks the connection.

```markdown
| MCP server name | Server URL | Type | Default mode |
|---|---|---|---|
| `DesignerStudio` | https://example.com/api/mcp/server/123 | test | balanced=10 |
```

- MCP server name: the part of the tool name between `mcp__` and the next `__`.
- Type: `dev`, `test`, or `prod`.
- Default mode: `strict`, `balanced[=N]`, or `full`. The build skill uses this value when the user gives no mode argument.

## `mcp-notes.md`

Record connector-specific differences from the tool descriptions: missing or failing tools, rejected keys, and workarounds. Add one dated line per new fact:

```text
- YYYY-MM-DD <tool>: <fact>
```
