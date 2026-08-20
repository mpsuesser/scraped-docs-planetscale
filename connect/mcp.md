---
url: https://planetscale.com/docs/connect/mcp
title: "Mcp"
description: ""
access_date: 2026-08-20T18:00:05.296Z
current_date: 2026-08-20T18:00:05.296Z
---

## What is the PlanetScale MCP server?

- A hosted MCP server that accesses your PlanetScale organizations, databases, branches, schema, and Insights data.
- Authenticated via OAuth. [Service tokens](mcp-service-token.md) are also supported for CI and headless clients.
- Accessible from any MCP client that supports HTTP-hosted servers.

## Quick start

## Cursor

One-click install

## VS Code

One-click install

## Claude Code

See instructions

## OpenCode

See instructions

## Codex

See instructions

## Notion

See instructions

## Other clients

See all options

## Authentication

The hosted MCP server uses **OAuth** so you can authorize access to PlanetScale organizations, databases, branches, and Insights data directly in your client via the MCP server.

- Each client (for example, Claude Code, Cursor, or other MCP-compatible tools) registers as an OAuth application with PlanetScale.
- When you connect the MCP server from your client, you’re redirected to PlanetScale to sign in and grant access.

**Server URL:**

```text
https://mcp.pscale.dev/mcp/planetscale
```

If you only need access to Insights data and Schema Recommendations (no query execution), use the insights-only server: `https://mcp.pscale.dev/mcp/planetscale-insights-only`. This server excludes the `planetscale_execute_read_query` and `planetscale_execute_write_query` tools.

For CI, headless agents, or any client that can send a custom HTTP header, see [MCP service tokens](mcp-service-token.md).

## Security and credentials

- Each query uses short-lived, ephemeral credentials that are created on demand and deleted immediately after execution.

We advise caution when giving LLMs *write* access to any production database. Always carefully review queries before execution.

### Scopes and permissions

OAuth permissions are controlled through scopes:

- Scopes define which organizations, databases, branches, or features the MCP server can see.
- You choose whether the MCP server has no access, read-only access, or full access to databases at the organization or per-database level.

Most MCP clients provide a way to re-authenticate the MCP server if you need to update your permissions.

Service token access is not chosen in the OAuth prompt. Grant it on the token. See [MCP service tokens](mcp-service-token.md).

## Installation instructions

### Cursor

## Cursor

Click to install the MCP server configuration for Cursor.

Manual installation:

1. Open the command palette and type “Cursor Settings”
2. Under “Tools & MCP” click “New MCP Server”
3. Paste the following JSON into the configuration file that opens

.cursor/mcp.json

```json
{
  "mcpServers": {
    "planetscale": {
      "url": "https://mcp.pscale.dev/mcp/planetscale"
    }
  }
}
```

4. Once this configuration is saved, Cursor will attempt to authenticate and show a login prompt. Select this prompt to grant Cursor access to your PlanetScale account.
5. You may need to restart Cursor to load the new configuration.

### VS Code

## VS Code

Click to install the MCP server configuration for VS Code.

Use the button above to launch VS Code and configure the PlanetScale MCP server automatically. Alternatively, follow the steps below to do it manually:

1. Open the Command Palette (`Ctrl+Shift+P` on Windows/Linux or `Cmd+Shift+P` on macOS)
2. Type “MCP: Add Server”
3. Choose “HTTP”
4. Enter the following details:
	- URL: [https://mcp.pscale.dev/mcp/planetscale](https://mcp.pscale.dev/mcp/planetscale)
		- Name: “PlanetScale”
5. Click “Add”

**Authorization**

After adding the PlanetScale MCP server you will need to start the server and authorize:

1. Open the Command Palette (`Ctrl+Shift+P` on Windows/Linux or `Cmd+Shift+P` on macOS)
2. Type “MCP: List Servers”
3. Select “PlanetScale”
4. Click “Start Server”
5. When the dialog appears saying “The MCP Server Definition ‘PlanetScale’ wants to authenticate to PlanetScale MCP,” click “Allow”
6. A popup will ask “Do you want Code to open the external website?” — click “Cancel”
7. You’ll see a message: “Having trouble authenticating to ‘PlanetScale MCP’? Would you like to try a different way? (URL Handler)”
8. Click “Yes”
9. Click “Open” and complete the PlanetScale sign-in flow to connect to PlanetScale MCP

### OpenCode

OpenCode supports automatic authentication for OAuth-based remote MCP servers.

1. Ensure [OpenCode](https://opencode.ai/) is installed and available in your terminal

```shellscript
opencode --version
```

2. Add the PlanetScale MCP server to your OpenCode configuration file.

~/.config/opencode/opencode.json

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "planetscale": {
      "type": "remote",
      "url": "https://mcp.pscale.dev/mcp/planetscale"
    }
  }
}
```

OpenCode configuration files can be stored inside a project or globally, see the [OpenCode configuration file](https://opencode.ai/docs/config/#precedence-order) documentation for more information.

3. Enter the following to authenticate with the PlanetScale MCP server

```shellscript
opencode mcp auth planetscale
```

4. Enter OpenCode

```shellscript
opencode
```

5. List the available MCP servers with the `/mcp` slash command.

```shellscript
/mcp
```

You should see the PlanetScale MCP server listed and connected.

```shellscript
planetscale connected
```

### Claude.ai and Claude for desktop

The PlanetScale MCP server is available as a connector in the [Claude Connectors Directory](https://claude.com/connectors/planetscale).

1. Navigate to **Settings > Connectors**
2. Click **Browse connectors**
3. Search for “PlanetScale”
4. Click **+**
5. Follow the prompts to authenticate with your PlanetScale account

Alternatively, install it as a custom connector:

1. Click **Add custom connector** at the bottom of the section
2. Enter the PlanetScale MCP server URL: `https://mcp.pscale.dev/mcp/planetscale`
3. Click **Add** to save the connector
4. Follow the prompts to authenticate with your PlanetScale account

Custom connectors using remote MCP are not available on all plans. See the [Claude Support page](https://support.claude.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp) for more information.

To use the connector in a conversation:

1. Click the **+** button in the lower left of your chat interface
2. Select **Connectors**
3. Toggle the PlanetScale connector on for that conversation

### Claude Code

The PlanetScale MCP server is available as a [Claude Code plugin](https://github.com/planetscale/claude-plugin). You must first add the PlanetScale marketplace, and then install the plugin.

```text
/plugin marketplace add planetscale/claude-plugin
/plugin install planetscale@planetscale
```

Alternatively, you can add the MCP server directly to your Claude Code configuration:

1. Ensure [Claude Code](https://code.claude.com/docs/en/overview) is available in your terminal

```shellscript
claude --version
```

2. Run the following command to add the PlanetScale MCP server to your Claude Code

```shellscript
claude mcp add --transport http "planetscale" https://mcp.pscale.dev/mcp/planetscale
```

3. Enter Claude Code

```shellscript
claude
```

4. List the available MCP servers with the `/mcp` slash command.

```shellscript
/mcp
```

You should see the PlanetScale MCP server listed in need of authentication. Select it, press “Enter” and follow the instructions in the browser to authenticate.

```shellscript
❯ planetscale · △ needs authentication
```

You should see the following message once authenticated:

```shellscript
❯ /mcp
  ⎿  Authentication successful. Connected to planetscale.
```

### Claude Managed Agents

Connect the PlanetScale MCP server to [Claude Managed Agents](https://platform.claude.com/) using a credential vault.

1. Go to [platform.claude.com](https://platform.claude.com/)
2. Open **Credential vaults** → **Create vault** → **Add credential**
3. Select **MCP OAuth**
4. Enter the server URL: `https://mcp.pscale.dev/mcp/planetscale`
5. Leave **Access token** and **OAuth client credentials** blank
6. Click **Connect**
7. Complete the PlanetScale sign-in flow when prompted

### Gemini CLI

1. Ensure [Gemini CLI](https://geminicli.com/) is available in your terminal

```shellscript
gemini --version
```

2. Run the following command to add the PlanetScale MCP server to your Gemini CLI

```shellscript
gemini mcp add --transport http "planetscale" https://mcp.pscale.dev/mcp/planetscale
```

3. Enter Gemini CLI

```shellscript
gemini
```

4. List the available MCP servers with the `/mcp` slash command and select “List”

```shellscript
/mcp list
```

You should see the PlanetScale MCP server listed in need of authentication.

```shellscript
🔴 planetscale - Disconnected (OAuth not authenticated)
```

Now run the following command to authenticate:

```shellscript
/mcp auth planetscale
```

You should see the following message once authenticated:

```shellscript
ℹ ✅ Successfully authenticated with MCP server 'planetscale'!
```

### Codex CLI

1. Ensure [Codex CLI](https://developers.openai.com/codex/cli/) is available in your terminal

```shellscript
codex --version
```

2. Run the following command to add the PlanetScale MCP server to your Codex CLI

```shellscript
codex mcp add planetscale --url https://mcp.pscale.dev/mcp/planetscale
```

You should be immediately prompted to authenticate. Follow the instructions in the browser to authenticate.

3. Enter Codex CLI

```shellscript
codex
```

4. List the available MCP servers with the `/mcp` slash command

```shellscript
/mcp
```

You should see the PlanetScale MCP server listed, enabled and authenticated.

```shellscript
• planetscale
  • Status: enabled
  • Auth: OAuth
```

### Amp CLI

1. Ensure [Amp CLI](https://ampcode.com/) is available in your terminal

```shellscript
amp --version
```

2. Run the following command to add the PlanetScale MCP server to your Amp CLI

```shellscript
amp mcp add planetscale https://mcp.pscale.dev/mcp/planetscale
```

3. Enter Amp CLI

```shellscript
amp
```

4. You should be immediately prompted to authenticate. Follow the instructions in the browser.
5. List the available MCP servers with the `/mcp list tools` command.

```shellscript
/mcp list tools
```

You should see the PlanetScale MCP server listed and its available tools.

### Notion

You can access the PlanetScale MCP server from inside a Notion Custom Agent.

1. Create a new Notion Agent or open an existing one
2. In the Custom Agent open “Settings” and scroll to “Tools and access”
3. Click “Add connection”
4. Insert the MCP server URL: `https://mcp.pscale.dev/mcp/planetscale`
5. Ensure Authentication is set to OAuth
6. Click “Connect”
7. Complete the authentication process to give the Notion Custom Agent access to specific organizations and databases

## Help your agent target your project’s database

By default, the MCP server is likely to scan through all your organizations and databases to find those relevant to your query. If you are working within a project that targets a single database, it’s a good idea to include this information in your `AGENTS.md` file to skip these tool calls.

For example:

AGENTS.md

```markdown
## PlanetScale Database

- **Organization:** \`my-organization\`
- **Database:** \`my-database\`
- **Branch:** \`main\`
```

## Query handling

All queries execute over HTTP via the hosted MCP server. This makes the MCP server useful in environments that normally cannot run database queries directly, such as browser-based tools or sandboxed AI agents.

The PlanetScale MCP server includes several built-in behaviors to help ensure safe, observable, and performant query execution.

### Read queries

- Read queries route to a replica when your branch has replicas configured, reducing load on the primary. This is the default behavior.
- Set `use_replica` to `false` on `planetscale_execute_read_query` to run against the primary instead. Works for both Vitess and Postgres databases.
- All queries include a `source=planetscale-mcp` comment, making them easy to identify and track in [Insights](../what-is-planetscale.md#insights).

#### Postgres row-level security

For Postgres databases, read queries run with a short-lived role that has `pg_read_all_data`. This role does not bypass row-level security (RLS).

When a read query returns zero rows or a zero count (such as `SELECT COUNT(*)` returning 0), the MCP server checks whether RLS is active on tables you can access. If so, the response includes a `warnings` array with code `postgres_rls_active`, listing the affected relations. A zero-row result does not necessarily mean the table is empty — RLS may be hiding rows from the MCP role.

### Write query safeguards

To help prevent accidental data loss, the MCP server blocks certain operations:

- `UPDATE` or `DELETE` statements without a `WHERE` clause are blocked.
- `TRUNCATE` statements are blocked.
- DDL statements (`CREATE`, `DROP`, `ALTER`, etc.) prompt the LLM to request human confirmation before proceeding.

### Postgres database targeting

For PlanetScale Postgres databases, the MCP server connects to the default `postgres` database by default. If you have created additional databases in the same cluster using `CREATE DATABASE`, you can specify which database to query using the `postgres_database_name` parameter.

This parameter is available on both the `planetscale_execute_read_query` and `planetscale_execute_write_query` tools. When omitted, queries run against the default `postgres` database for your branch.

## Example workflows

Once installed, you can ask your MCP-enabled editor or agent to:

- “Show me all databases in my PlanetScale organization and highlight anything running on PlanetScale Metal.”
- “List the branches for my production database and summarize their differences.”
- “Look at my slowest queries over the last day and suggest index or query changes.”
- “Check whether the CPU and memory profile for my database is appropriate for the current workload.”
- “Explain what changed between yesterday’s and today’s query patterns in Insights.”

You can also run these workflows on a recurring schedule using [Cursor Automations](https://cursor.com/docs/cloud-agent/automations). See [Self-improving database](self-improving-database.md) for ready-to-use prompts.

## Available tools

The hosted PlanetScale MCP server includes a curated set of tools designed for day-to-day database exploration, debugging, and insights.

The available tools are:

1. `planetscale_list_organizations` - List all PlanetScale organizations you have access to.
2. `planetscale_list_databases` - List all databases within an organization.
3. `planetscale_list_branches` - List all branches within a database.
4. `planetscale_get_organization` - Get details about a specific organization.
5. `planetscale_get_database` - Get details about a specific database.
6. `planetscale_get_branch` - Get details about a database branch.
7. `planetscale_get_branch_schema` - Get the schema for a database branch.
8. `planetscale_execute_read_query` - Execute a read-only SQL query (SELECT, SHOW, DESCRIBE, EXPLAIN) against a PlanetScale database. Optionally accepts `use_replica` to route to a replica (default) or primary. For Postgres, optionally accepts `postgres_database_name` to target a non-default database. Returns RLS warnings when policies may filter results.
9. `planetscale_execute_write_query` - Execute a write SQL query (INSERT, UPDATE, DELETE, or DDL) against a PlanetScale database. For Postgres, optionally accepts `postgres_database_name` to target a non-default database.
10. `planetscale_get_insights` - Get query performance insights for a PlanetScale database branch.
11. `planetscale_list_regions_for_organization` - List all available regions for an organization.
12. `planetscale_list_cluster_size_skus` - List all available cluster size SKUs.
13. `planetscale_list_invoices` - List all invoices for an organization.
14. `planetscale_get_invoice_line_items` - Get all line items for an invoice, with prorated costs broken down by database branch.
15. `planetscale_search_documentation` - Search the PlanetScale documentation.
16. `planetscale_list_schema_recommendations` - List schema recommendations for a database, including suggestions for adding indexes, removing redundant indexes, and preventing primary key exhaustion.

The MCP server tools are [open source and available on GitHub](https://github.com/planetscale/mcp-server).

## Troubleshooting

If your MCP client cannot connect or tools fail to run:

1. **Restart your client or CLI tool**
	After installation, you may need to restart your client or CLI tool to load the new MCP configuration.
2. **Check the server URL**
	Make sure the MCP server URL is set to the hosted endpoint (for example, `https://mcp.pscale.dev/mcp/planetscale`).
3. **Re-authorize the MCP server**
	If scopes or tokens have changed, reauthorize the PlanetScale MCP server in your client so it can request fresh tokens.
4. **Verify organization and database access**
	Confirm that your PlanetScale user account has access to the orgs and databases you expect to see.
5. **Using a service token**
	If you authenticated with a service token instead of OAuth, see [MCP service tokens](mcp-service-token.md).

## PlanetScale CLI MCP server

The local-only `pscale mcp` MCP server has been removed. Use the hosted MCP server documented above.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
