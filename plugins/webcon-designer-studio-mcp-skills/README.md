# `webcon-designer-studio-mcp-skills`

Skills for setting up and building WEBCON applications through the DesignerStudio MCP server. Each skill handles one job. They do not require the `webcon-designer-studio-mod` plugin; if it is installed, the build uses its "Manual tasks" pane.

## Skills

Run the skills manually. In the slash menu, their full names include the plugin prefix, for example `/webcon-designer-studio-mcp-skills:webcon-env-setup`. See [Environment files](references/environment-files.md) for details on what they read and write.

| Skill | Job | When to run it | Output | Calls the MCP |
|---|---|---|---|---|
| `/webcon-env-setup` | Connect and register a connector; agree the team conventions | Start here for each new environment connection (see [How to start](#how-to-start)). Conventions: once per team, again when they change. | Row in `environments.md`; `design-defaults.md` | Read only: one connection check, and the observe option |
| `/webcon-env-discover` | Report the conventions that an environment already uses | Optional, before the conventions walkthrough. `/webcon-env-setup` runs it for the observe option. | `observed-conventions.md` | Read only |
| `/webcon-app-design` | Turn requirements, an idea or a change request into a build plan | Once per application or change, before the build | Plan document, approved by you | Read only |
| `/webcon-app-build [strict \| balanced[=N] \| full]` | Build, verify and publish from an approved plan | For each build. The mode controls when open manual tasks stop the build. | Configuration, `guids.md`, build logs, manual tasks | Read and write |

Order of use: `webcon-env-setup` (once) → `webcon-app-design` (per application) → `webcon-app-build` (per build) → manual tasks → publish.

### Conventions and plans

Conventions (`design-defaults.md`) are the choices of your team: names, modelling, form layout, field matrix, task assignment, diagram layout, documentation. The plugin only proposes them ([configuration conventions proposal](skills/webcon-env-setup/configuration-conventions-proposal.md)); in the walkthrough of `webcon-env-setup`, your team keeps, changes or removes each section. They apply to all applications.

A plan is the design of one application: processes, steps, fields, rules, automations, manual tasks and build groups. `webcon-app-design` writes it with a fixed procedure and applies the conventions. The plan records only the deviations from them.

`webcon-app-build` builds from the plan. With a full description (as in the examples), you can skip the separate design run: the build does only the last steps of the design procedure (environment facts, capability check, manual tasks, build groups, approval).

## How to start

1. In Claude Code, install the plugin:

   ```text
   /plugin install webcon-designer-studio-mcp-skills@webcon-marketplace
   ```

2. Enter the [connection values](#connection).
3. Run `/webcon-env-setup`. It checks the connection and registers the connector. On the first run, it also sets the design defaults, either from values you provide or by observing the environment. If the connection is not ready, it tells you how to fix it.
4. Run `/webcon-app-design` with your requirements or idea. Review the plan, the open decisions and the manual tasks. Approve the scope or ask for changes before the agent writes anything.
5. Run `/webcon-app-build` with the approved plan. For your first build, use a `dev` environment and follow the [example instructions](skills/webcon-app-build/examples/README.md): the examples are full descriptions, so you can give them to `/webcon-app-build` without step 4.
6. When prompted, do the manual tasks in Designer Studio, then let the agent validate and publish.

## Connection

The plugin ships `.mcp.json`, which registers its default MCP connection under the name `DesignerStudio`.

When you enable the plugin, Claude Code asks for four values. They are the same for on-prem and cloud WEBCON:

| Field | Meaning | Example |
|---|---|---|
| DesignerStudio MCP server URL | MCP endpoint (the `/docs` link with `/docs` stripped) | `https://mcp.webconbps.com/api/mcp/server/<server id>` |
| WEBCON OAuth client ID | Client ID of the pre-registered OAuth client | issued per tenant |
| WEBCON OAuth client secret | Stored in the OS keychain | issued per tenant |
| WEBCON OAuth scopes | Exact scopes registered for the client (read them from the server `/docs` page or `/.well-known/oauth-protected-resource`) | `Mcp.Tools User.Designer.ReadWrite.All openid profile email` |

Use the server's exact scopes. Otherwise, Claude requests all advertised scopes, and the login can fail.

### Administrator checklist (WEBCON)

Register the API application in **Admin Panel → Integrations → API → New API application**:

- Application type: **user context**. An application context application does not support the authorization code grant.
- Authentication type: **Authorization code** (with PKCE).
- Authorized redirect URI: `http://localhost:8123/callback`. The plugin pins port 8123 with `callbackPort` in `.mcp.json`. The port in WEBCON and in `.mcp.json` must match.
- Authorization flows configuration: tick **Allow offline access (issue Refresh Tokens)**. Claude adds `offline_access` itself; do not add it to the scopes field.

To change the values later, open `/plugin`, select this plugin and re-enter the configuration.

### More than one environment

The plugin registers one connection. For each other environment, run `/webcon-env-setup`; it tells you how to add the connection. If multiple connectors are available, specify which one the build should use.
