---
url: https://planetscale.com/docs/connect/mcp-service-token
title: "Mcp Service Token"
description: ""
access_date: 2026-08-14T22:51:42.506Z
current_date: 2026-08-14T22:51:42.506Z
---

OAuth is the default for the [hosted MCP server](mcp.md). Use a [service token](../api/service-tokens.md) when you cannot complete a browser login, such as CI or a headless agent.

The hosted MCP server accepts the token secret as a Bearer token.

1. Create a service token in your organization settings.
2. Copy the **token** value. It starts with `pscale_tkn_`. Do not use the token ID, and do not send `id:secret`.
3. Grant only the [organization and database permissions](../api/service-tokens.md#assign-service-token-permissions) you want the MCP tools to have. Leave the rest off.
4. Set `PLANETSCALE_API_TOKEN` to that token value in the environment where the client runs.
5. Send it as `Authorization: Bearer pscale_tkn_...` to `https://mcp.pscale.dev/mcp/planetscale`.

The same header works for the insights-only server: `https://mcp.pscale.dev/mcp/planetscale-insights-only`.

Use the token secret only. `Authorization: Bearer <id>:<secret>` is rejected. Do not commit the token in a config file.

Claude.ai, Claude for desktop, Claude Managed Agents, and Notion authenticate with OAuth only. They do not accept a service token header.

## Permissions

The MCP server calls the PlanetScale API with the token you send. Each tool fails if the token is missing the permission for that API call. Grant the smallest set that covers the tools you want.

Add organization permissions on the token, then add database permissions on each database the token should see, or use [permissions for all databases](../api/service-tokens.md#add-permissions-for-all-databases).

### Read-only access

This covers listing organizations, databases, branches, schema, Insights, and schema recommendations. It does not allow SQL execution.

**Organization**

- `read_organization`
- `read_databases`

**Database**

- `read_database`
- `read_branch`

`read_database` returns metadata about a database (name, state, default branch). It does not connect to MySQL or Postgres, and it does not run queries. Connecting and executing SQL requires the `connect_*` permissions below.

`read_databases` lets the token list every database in the organization. If you only want one database, skip `read_databases` and grant `read_database` plus `read_branch` on that database only.

Add `read_invoices` on the organization if you want the invoice tools.

### Read queries

Add these database permissions on top of the read-only set. The MCP server creates a short-lived reader password or Postgres role, runs the query, then deletes the credential.

- `connect_branch` (development branches)
- `connect_production_read_only_branch` (production branches)
- `delete_branch_password` (development branches)
- `delete_production_read_only_branch_password` (production branches)

If you only query production, you can leave off `connect_branch` and `delete_branch_password`. If you only query development branches, leave off the production pair. `delete_production_branch_password` also works for deleting reader credentials on production.

Do not grant `connect_production_branch`. That permission can create write credentials on production.

### Write queries

`planetscale_execute_write_query` creates an admin or write role, so it needs the full connect permissions, not the read-only ones.

Add these on top of the read-only set:

- `connect_branch`
- `connect_production_branch`
- `delete_branch_password`
- `delete_production_branch_password`

The full connect permissions can also run read queries. You do not need the read-only connect pair if you already grant these.

Write query permissions can run `INSERT`, `UPDATE`, `DELETE`, and DDL. Only grant them on databases where that is acceptable.

### Tool permissions

| Tools | Organization permissions | Database permissions |
| --- | --- | --- |
| `planetscale_list_organizations` | None. Any valid token can list the organization it belongs to. |  |
| `planetscale_get_organization`, `planetscale_list_regions_for_organization`, `planetscale_list_cluster_size_skus` | `read_organization` |  |
| `planetscale_list_databases`, `planetscale_get_database` | `read_databases` to see every database. Otherwise grant `read_database` on each database. | `read_database` |
| `planetscale_list_branches`, `planetscale_get_branch`, `planetscale_get_branch_schema` |  | `read_branch` |
| `planetscale_get_insights`, `planetscale_list_schema_recommendations` | `read_databases` also works in place of `read_database` | `read_database` and `read_branch` |
| `planetscale_list_invoices`, `planetscale_get_invoice_line_items` | `read_invoices` |  |
| `planetscale_execute_read_query` |  | Read-only set, plus the read-query connect and delete permissions above |
| `planetscale_execute_write_query` |  | Read-only set, plus the write-query connect and delete permissions above |
| `planetscale_search_documentation` | None | None |

## Client configuration

#### Cursor

Add the header to `.cursor/mcp.json` or `~/.cursor/mcp.json`. Cursor and the Cursor CLI (`agent`) read `PLANETSCALE_API_TOKEN` from the environment at connect time.

.cursor/mcp.json

```json
{
  "mcpServers": {
    "planetscale": {
      "url": "https://mcp.pscale.dev/mcp/planetscale",
      "headers": {
        "Authorization": "Bearer ${env:PLANETSCALE_API_TOKEN}"
      }
    }
  }
}
```

#### Claude Code

```shellscript
claude mcp add --transport http "planetscale" https://mcp.pscale.dev/mcp/planetscale \
  --header "Authorization: Bearer ${PLANETSCALE_API_TOKEN}"
```

The shell expands the variable when you run the command, so the token is written into your Claude MCP config. Don’t commit that file.

#### Codex

Codex reads the env var name and sends it as a Bearer token. The variable should be the `pscale_tkn_...` secret, not `Bearer ...`.

```shellscript
codex mcp add planetscale \
  --url https://mcp.pscale.dev/mcp/planetscale \
  --bearer-token-env-var PLANETSCALE_API_TOKEN
```

Or in `~/.codex/config.toml`:

```toml
[mcp_servers.planetscale]
url = "https://mcp.pscale.dev/mcp/planetscale"
bearer_token_env_var = "PLANETSCALE_API_TOKEN"
```

#### VS Code

Add the header to `.vscode/mcp.json`. VS Code reads `PLANETSCALE_API_TOKEN` from the environment at connect time.

.vscode/mcp.json

```json
{
  "servers": {
    "planetscale": {
      "type": "http",
      "url": "https://mcp.pscale.dev/mcp/planetscale",
      "headers": {
        "Authorization": "Bearer ${env:PLANETSCALE_API_TOKEN}"
      }
    }
  }
}
```

#### Gemini CLI

```shellscript
gemini mcp add --transport http \
  --header "Authorization: Bearer ${PLANETSCALE_API_TOKEN}" \
  planetscale https://mcp.pscale.dev/mcp/planetscale
```

The shell expands the variable when you run the command, so the token is written into your Gemini MCP config. Don’t commit that file.

#### OpenCode

Set `oauth` to `false` so OpenCode uses the header instead of starting a browser login.

~/.config/opencode/opencode.json

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "planetscale": {
      "type": "remote",
      "url": "https://mcp.pscale.dev/mcp/planetscale",
      "oauth": false,
      "headers": {
        "Authorization": "Bearer {env:PLANETSCALE_API_TOKEN}"
      }
    }
  }
}
```

## Troubleshooting

If a tool returns `invalid_token`, the header is the wrong shape. Send `Authorization: Bearer pscale_tkn_...` (the token secret only). The token ID, or `id:secret`, is rejected.

If a tool returns permission denied, the token is missing the grant for that API call. Add the permission from the table above.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
