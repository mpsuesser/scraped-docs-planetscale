---
url: https://planetscale.com/docs/cli
title: "Cli"
description: ""
access_date: 2026-08-18T22:25:53.110Z
current_date: 2026-08-18T22:25:53.110Z
---

To interact with PlanetScale and manage your databases, you can use the `pscale` CLI to do the following:

- Create, delete and list your databases and branches
- Run non-interactive SQL for agents and scripts (`pscale sql`)
- Open a secure MySQL or PostgreSQL shell instance
- Manage your deploy requests
- Bootstrap AI agents (`pscale agent-guide`)
- …and more!

## Install pscale

Install or upgrade the PlanetScale CLI for macOS, Linux, or Windows.

Agents and automation should start with the [Agent setup prompt](agent-setup/prompt.md) or `pscale agent-guide --format json`. Agent automation commands require `pscale` 0.292.0 or later; run `brew upgrade pscale` if `agent-guide` is unknown. Always pass `--format json` in automation.

`pscale` can use the MySQL command-line client to quickly open an interactive shell for a database branch. Optional instructions for installing the MySQL client can be found for each platform below.

## Getting Started

Make sure to first [set up your PlanetScale developer environment](cli/planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## Available Commands

Use `pscale [command] [command]` to start up the `pscale` CLI in your terminal.

| **Command** | **Subcommands/Options** | **Flags** | **Product** | **Description** |
| --- | --- | --- | --- | --- |
| [`agent-guide`](cli/agent-guide.md) |  | `--format json` | Vitess, Postgres | Bootstrap JSON for AI agents; embeds CLI conventions and skills/MCP hints |
| [`api`](cli/api.md) |  | `--help`, `--org string`, `--database string`, `--branch string`, `--field key=value`, `--header stringArray`, `--input string`, `--method string`, `--query key=value` | Vitess, Postgres | Performs authenticated calls against the PlanetScale API and prints the response to stdout. |
| [`audit-log`](cli/audit-log.md) | `list` | `--help`, `--org string` | Vitess, Postgres | List all [audit logs](security/audit-log.md#review-your-organization-audit-log) |
| [`auth`](cli/auth.md) | `login`, `logout`, `check` | `--help`, `--format json` | Vitess, Postgres | Authenticate via console or JSON device login for agents |
| [`backup`](cli/backup.md) | `create`, `delete`, `list`, `restore`, `show` | `--help`, `--org string` | Vitess, Postgres | Manage [branch backups](vitess/backups.md) |
| [`branch`](cli/branch.md) | `connections`, `create`, `delete`, `diff`, `keyspaces`, `list`, `promote`, `query-patterns`, `refresh-schema`, `schema`, `show`, `switch`, `vschema` | `--help`, `--org string` | Vitess, Postgres | Manage [branches](vitess/schema-changes/branching.md) |
| [`completion`](cli/completion.md) | `bash`, `zsh`, `fish`, `powershell` | `--help` | Vitess, Postgres | Generate completion script for specified shell |
| [`connect`](cli/connect.md) | `<database_name>` `<branch_name>` | `--execute string`, `--execute-env-url string`, `--execute-protocol string`, `--help`, `--host string`, `--org string`, `--port string`, `--remote-addr string`, `--role string` | Vitess | Create a [secure connection](vitess/tutorials/connect-any-application.md#option-2-connect-using-the-planetscale-proxy) to the given database and branch |
| [`database`](cli/database.md) | `create`, `delete`, `dump`, `list`, `restore-dump`, `show` | `--help` | Vitess, Postgres | Manage databases |
| [`deploy-request`](cli/deploy-request.md) | `apply`, `cancel`, `close`, `create`, `deploy`, `diff`, `edit`, `force-cutover`, `list`, `revert`, `review`, `show`, `skip-revert` | `--help` | Vitess | Manage [deploy requests](vitess/schema-changes/deploy-requests.md#create-a-deploy-request) including [gated deployments](vitess/schema-changes/deploy-requests.md#gated-deployments) |
| `help` | `agent-guide`, `audit-log`, `auth`, `backup`, `branch`, `completion`, `connect`, `database`, `deploy-request`, `help`, `import`, `insights`, `inspect`, `mcp`, `metrics`, `org`, `password`, `ping`, `region`, `role`, `service-token`, `shell`, `signup`, `size`, `sql`, `traffic-control`, `webhook`, `workflow` | `--help` | Vitess, Postgres | View help for any command |
| [`import`](cli/import.md) | `d1 doctor`, `d1 lint`, `d1 convert-schema`, `d1 start`, `d1 verify`, `d1 status`, `d1 complete` | `--help`, `--org string` | Postgres | Import external databases ([Cloudflare D1](postgres/imports/postgres-imports.md)) into PlanetScale Postgres |
| [`insights`](cli/insights.md) | `queries`, `errors`, `anomalies`, `recommendations` | `--help`, `--org string`, `--format json`, `--sort string`, `--dir string`, `--limit int`, `--period string` | Vitess, Postgres | Server-side query insights, anomalies, and schema recommendations from production traffic |
| [`inspect`](cli/inspect.md) | `all`, `table-sizes`, `index-sizes`, `unused-indexes`, `redundant-indexes`, `invalid-indexes`, `seq-scans`, `long-running-queries`, `locks`, `outliers`, `calls`, `bloat`, `vacuum-stats`, `replication-slots`, `subscriptions` | `--help`, `--org string`, `--format json`, `--keyspace string`, `--dbname string`, `--role string`, `--replica` | Vitess, Postgres | Live, read-only diagnostic checks over a direct database connection |
| [`org`](cli/org.md) | `list`, `show`, `switch` | `--help` | Vitess, Postgres | Manage and switch [organizations](security/access-control.md) |
| [`mcp`](cli/mcp.md) | `install`, `server` | `--target string (cursor\|claude-code\|zed)`, `--format json`, `--help` | Vitess, Postgres | Install hosted MCP config ([docs](connect/mcp.md)) |
| [`metrics`](cli/metrics.md) | `show`, `instant`, `report` | `--help`, `--org string`, `--format json`, `--metric string`, `--period string`, `--from string`, `--to string`, `--steps int` | Vitess, Postgres | Query historical and current branch metrics from PlanetScale’s metrics service |
| [`password`](cli/password.md) | `create`, `delete`, `list` | `--help`, `--org string` | Vitess | Manage [branch credentials](vitess/connecting/connection-strings.md) |
| [`ping`](cli/ping.md) |  | `--help`, `--count, -n int`, `--concurrency int`, `--provider, -p string` `--timeout duration` | Vitess, Postgres | Check [latency](vitess/connecting/network-latency.md) between your machine and PlanetScale’s public regions |
| [`region`](cli/region.md) | `list` | `--org string` | Vitess, Postgres | View available [regions](https://planetscale.com/docs/vitess/regions) |
| [`role`](cli/role.md) | `create`, `delete`, `get`, `list`, `reassign`, `renew`, `reset`, `reset-default`, `update` | `--help`, `--org string`, `--inherited-roles string`, `--ttl duration`, `--force`, `--successor string`, `--name string`, `--web` | Postgres | Manage [Postgres roles](postgres/connecting/roles.md) |
| [`service-token`](cli/service-token.md) | `add-access`, `create`, `delete`, `delete-access`, `list`, `show-access` | `--help`, `--org string` | Vitess, Postgres | Manage access of [service tokens](api/reference/service-tokens.md) |
| [`size`](planetscale-plans.md) | `cluster list` | `--help`, `--org string`, `--region string`, `--metal` | Vitess, Postgres | View available [cluster sizes](planetscale-plans.md) |
| [`traffic-control`](cli/traffic-control.md) | `budget`, `rule` | `--help`, `--org string` | Postgres | Manage [Database Traffic Control](postgres/traffic-control.md) budgets and rules for a Postgres database branch |
| [`shell`](cli/shell.md) | `<database_name>` `<branch_name>` | `--help`, `--local-addr string`, `--org string`, `--remote-addr string`, `--role string`, `--replica` | Vitess, Postgres | Open an interactive shell to the specified database and branch |
| [`signup`](cli/signup.md) |  | `--help` | Vitess, Postgres | Sign up for a new PlanetScale account |
| [`sql`](cli/sql.md) | `<database>` `<branch>` | `--org string`, `--query string`, `--role string`, `--replica`, `--dbname string`, `--keyspace string`, `--force`, `--format json` | Vitess, Postgres | Execute a SQL query without an interactive shell (agents/scripts) |
| [`webhook`](cli/webhook.md) | `create`, `delete`, `list`, `show`, `test`, `update` | `--help`, `--org string`, `--events string`, `--url string`, `--enabled` | Vitess, Postgres | Manage [webhooks](api/webhooks.md) for databases |
| [`workflow`](cli/workflow.md) | `cancel`, `complete`, `create`, `cutover`, `list`, `retry`, `reverse-cutover`, `reverse-traffic`, `show`, `switch-traffic`, `verify-data` | `--help`, `--org string` | Vitess | Manage the workflows for PlanetScale databases |

## Flags

You may use the following flags with the PlanetScale CLI commands.

| **Flag** | **Description** |
| --- | --- |
| `--api-token string` | The API token to use for authenticating against the PlanetScale API |
| `--api-url string` | The base URL for the PlanetScale API. (default “ [https://api.planetscale.com/](https://api.planetscale.com/) “) |
| `--config string` | Config file *(default: `$HOME/.config/planetscale/pscale.yml`)* |
| `--debug` | Enable debug mode |
| `-f, --format string` | Show output in specific format. Possible values: *\[human, json, csv\] (default: “human”)* |
| `-h, --help` | Get more information about a command |
| `--no-color` | Disable color output |
| `--service-token string` | Service Token for authenticating |
| `--service-token-id string` | The Service Token ID for authenticating |
| `--version` | Show pscale version |

## Service tokens permissions

A complete list of access permissions available for use with service tokens can be found in the [PlanetScale API documentation](api/reference/service-tokens.md#access-permissions).

## Service token automation

Running `pscale` in CI or an AI agent uses a [service token](cli/service-tokens.md) instead of `pscale auth login`. Provide the token as environment variables (recommended) or as flags on each command; both are equivalent:

```shellscript
# Environment variables (recommended for CI and agents)
export PLANETSCALE_SERVICE_TOKEN_ID="<SERVICE_TOKEN_ID>"
export PLANETSCALE_SERVICE_TOKEN="<SERVICE_TOKEN>"

# Or per-command flags
pscale database list --org <org> \
  --service-token-id "<SERVICE_TOKEN_ID>" \
  --service-token "<SERVICE_TOKEN>"
```

A service token has **no active organization**, so resource commands need the org supplied explicitly. Set it with `PLANETSCALE_ORG`, the `--org` flag on the subcommand, or `pscale org switch <org>` (which works with a service token):

```shellscript
export PLANETSCALE_ORG="<org>"
pscale database list --format json          # uses PLANETSCALE_ORG
pscale database list --org <org> --format json   # explicit flag
```

`--org` goes on the **resource subcommand** (`database`, `branch`, `sql`, `api`, …), never on root `pscale`. `pscale --org <org> database list` fails with `unknown flag: --org`.

Per-command-family matrices (env-var auth, `--service-token` flag, required `--org`, Postgres/Vitess, `--format json`, API equivalent): [`org`](cli/org.md#service-token-automation-org) · [`service-token`](cli/service-token.md#service-token-automation-service-token) · [`database`](cli/database.md#service-token-automation-database) · [`branch`](cli/branch.md#service-token-automation-branch) · [`role`](cli/role.md#service-token-automation-role) · [`password`](cli/password.md#service-token-automation-password).

### Commands to avoid under a service token

Do not retry these with service-token auth. The failure is by design, not transient. This is the most common cause of agent retry loops.

| Command | Why | Do this instead | Exact error |
| --- | --- | --- | --- |
| `pscale org show` | A service token has no “current” organization | `pscale org list --format json`, then pass `--org` or `PLANETSCALE_ORG` | `not authenticated yet. Please run 'pscale auth login'` |
| `pscale service-token list` | Token management is blocked when authenticated with a token | `pscale api organizations/<org>/service-tokens --format json` | `pscale service-token list is unavailable when authenticated with a service token` |
| `pscale service-token show-access` | Same as `list` | `pscale api organizations/<org>/service-tokens/<id> --format json` | `pscale service-token show-access is unavailable when authenticated with a service token` |
| `pscale service-token` (other sub-commands) | Create/manage tokens blocked under service-token auth | `pscale auth login`, then CLI; or [Service tokens API](api/reference/service-tokens.md) | `pscale service-token <sub-command> is unavailable when authenticated with a service token` |
| `pscale auth login` / `logout` | Not needed; the token is the credential | Set the token env vars; run `pscale auth check --format json` to confirm | requires an interactive browser device flow |
| `pscale shell` / `connect` | Interactive sessions | Use [`pscale sql`](cli/sql.md) for non-interactive queries | opens an interactive session |

`pscale org show` prints `not authenticated yet. Please run 'pscale auth login'` under a valid service token. This is the **no-current-org** state, not an auth failure. Do **not** respond by running `pscale auth login`. Discover the org with `pscale org list` and pass it with `--org` or `PLANETSCALE_ORG`.

`pscale auth check --format json` confirms a token is wired up. Expect `"authenticated": true`, `"auth_method": "service_token"`, and (until an org is set) an `action_required` status with a `NO_ORG` issue and a non-zero exit code. Resolve it by passing `--org` / `PLANETSCALE_ORG` on your commands.

### pscale api fallback for hard-to-parse output

When a command’s human output is hard to parse, prefer `--format json`, or drop to the raw API with `pscale api`, which returns the API response verbatim and uses the same token:

```shellscript
# org list
pscale api organizations --format json

# database list / show
pscale api organizations/<org>/databases --format json
pscale api organizations/<org>/databases/<database> --format json

# branch list / show
pscale api organizations/<org>/databases/<database>/branches --format json
pscale api organizations/<org>/databases/<database>/branches/<branch> --format json

# service-token list / show-access (CLI blocked under service-token auth)
pscale api organizations/<org>/service-tokens --format json
pscale api organizations/<org>/service-tokens/<id> --format json
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
