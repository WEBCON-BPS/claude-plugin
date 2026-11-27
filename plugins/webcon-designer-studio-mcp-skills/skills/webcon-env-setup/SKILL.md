---
name: webcon-env-setup
description: Starting point for each new WEBCON environment connection - check that the DesignerStudio MCP connector is connected, register it (environment type, default mode), and write the team design defaults (`design-defaults.md`) the first time. Run for each new connector and when the conventions change.
disable-model-invocation: true
---

# Set up the connectors and the design defaults of WEBCON environments

This skill is the starting point for each new environment connection. Do it before `webcon-env-discover`, `webcon-app-design` or `webcon-app-build` use a connector.

Read the [environment files reference](../../references/environment-files.md) for file locations and formats. Ask the user if they want to use another location before you look for or write the files. If you start `webcon-env-discover`, give it this location.

Do not call the DesignerStudio MCP, except for the connection check below and the read-only calls of the observe option in the conventions walkthrough.

## Connect and register a connector (once for each new connector)

1. Find the DesignerStudio connectors of this session: the MCP servers that have DesignerStudio tools (for example `browse_applications`). The server name is the part of the tool name between `mcp__` and the next `__`. Show each one, and mark the ones that have a row in `environments.md`.
2. Ask which connector the user wants to set up. If it has a row, skip to the conventions walkthrough.
3. If the connector is not in the list, or its tools return auth errors, the connection is not ready. Tell the user how to connect, then stop. The user runs this skill again after the connection works:
   - The plugin's default connection (`DesignerStudio`): enter the connection values in `/plugin` (see the plugin README, Connection), then authenticate in `/mcp`.
   - Another environment: add a connection with a distinct name (for example `DesignerStudio-test`). If the `webcon-mcp-assistant` plugin is installed, `/webcon-mcp-assistant:webcon-mcp-setup` guides the setup and `/webcon-mcp-assistant:connect` makes the `claude mcp add-json` command. Restart Claude Code and authenticate in `/mcp`.
4. Check the connection with one read-only call: `browse_application_groups` on the connector. If it fails, go to step 3.
5. Ask for the server URL (the `/docs` link with `/docs` stripped).
6. Ask for the type and default mode using the values in the reference. Suggest `balanced=10` for `dev` and `test`, and `strict` for `prod`.
7. Show the new row. Add it to `environments.md` only after the user confirms it.
8. If `design-defaults.md` does not exist, continue with the conventions walkthrough. If it exists, stop unless the user asks for the walkthrough.

## Conventions walkthrough (once for all environments)

Keep the conventions independent of specific applications, processes, objects, and terms.

1. If `design-defaults.md` exists and the user does not ask for a full walkthrough: ask which sections changed, review only those (step 4), and keep the other sections and their dates. Then go to step 6.
2. Ask the user where the conventions come from:
   - Bring: the team has design defaults. Start from `design-defaults.md` if it exists, or from a document the user gives. Otherwise, use the [configuration conventions proposal](configuration-conventions-proposal.md) as a starting point.
   - Observe: read the conventions that the maintainers already use in a registered environment. Ask which connector to read. Follow the [discovery procedure](../webcon-env-discover/SKILL.md); it writes `observed-conventions.md`. Then start from `design-defaults.md` if it exists, otherwise from the proposal, and use the report in the review.

   If `observed-conventions.md` already exists, ask if the user wants to use it instead of a new discovery.

3. Ask the general questions:
   - Which language do names in the configuration use? Write the answer to `namesLanguage`.
   - Do the names need a prefix (for example a company or application code)? Write the answer as a rule in the Names section.
4. Review each H2 section of the starting document, with its H3 sub-sections:
   1. Show the section text. If there is an observed-conventions report, show the patterns mapped to the section next to it. Strong patterns are candidates for rules; conflicts are questions for the user.
   2. Get the user's decision for each section: keep, change, or remove it? The user can change the structure too. If the language of names is not English, ask for the name prefixes in that language (for example `Get …`, `Is … ?`, `Set {form field name}`).
   3. Keep or change: write the section with `as of <today>` (keep is a confirmation). Do not change the text of rules that the user keeps. Remove: delete the section.
5. Show the patterns of the report marked "new" (no section). Ask if the team has other conventions that the proposal does not cover. Add them as new sections.
6. Set `source` and `asOf` as the reference says. Show the full result. After the user confirms it, write it to `design-defaults.md`. Leave the proposal and the report unchanged.
