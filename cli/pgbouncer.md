---
url: https://planetscale.com/docs/cli/pgbouncer
title: "Pgbouncer"
description: ""
access_date: 2026-08-14T00:39:58.404Z
current_date: 2026-08-14T00:39:58.404Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The pgbouncer command

Manage [dedicated PgBouncers](../postgres/connecting/pgbouncer.md) for a Postgres database branch.

Dedicated PgBouncers run separately from the database nodes and provide connection pooling for primary or replica traffic. This is distinct from the local PgBouncer on port 6432 and from `pgbouncer.*` parameters on `pscale branch resize`.

This command is only available for PostgreSQL databases. **`--org` is required.**

**Usage:**

```shellscript
pscale pgbouncer <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Description** | **Product** |
| --- | --- | --- | --- |
| `list <DATABASE_NAME> <BRANCH_NAME>` |  | List dedicated PgBouncers for a branch | Postgres |
| `show <DATABASE_NAME> <BRANCH_NAME> <NAME>` |  | Show a dedicated PgBouncer | Postgres |
| `create <DATABASE_NAME> <BRANCH_NAME>` | `--target` \*, `--name`, `--size`, `--replicas-per-cell` | Create a dedicated PgBouncer | Postgres |
| `resize <DATABASE_NAME> <BRANCH_NAME> <NAME>` | `--size`, `--replicas-per-cell`, `--target`, `--parameters`, `--wait`, `--wait-timeout` | Change size, replicas, target, or parameters | Postgres |
| `resize status <DATABASE_NAME> <BRANCH_NAME> <NAME>` |  | Show the latest resize request | Postgres |
| `resize cancel <DATABASE_NAME> <BRANCH_NAME> <NAME>` |  | Cancel unfinished resize requests | Postgres |
| `delete <DATABASE_NAME> <BRANCH_NAME> <NAME>` | `--force` | Delete a dedicated PgBouncer | Postgres |

> \* *Flag is required*

#### Sub-command flag descriptions

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--name` | Name for the PgBouncer. Optional on create; auto-generated if omitted. | `create` |
| `--target` | Traffic target: `primary`, `replica`, or `replica_az_affinity`. Required on create. | `create`, `resize` |
| `--size` | PgBouncer size SKU (for example `PGB_10`). | `create`, `resize` |
| `--replicas-per-cell` | Number of PgBouncer replica servers per cell. | `create`, `resize` |
| `--parameters` | Set a PgBouncer parameter as `namespace.name=value` (for example `pgbouncer.default_pool_size=100`). Repeatable. | `resize` |
| `--wait` | Wait for the resize request to complete before returning. | `resize` |
| `--wait-timeout` | Maximum time to wait with `--wait`. Default: `10m`. | `resize` |
| `--force` | Delete a PgBouncer without confirmation. | `delete` |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `pgbouncer` command |
| `--org <ORGANIZATION_NAME>` | The organization for the current user (**required**) |

### Global flags

| **Command** | **Description** |
| --- | --- |
| `--api-token <TOKEN>` | The API token to use for authenticating against the PlanetScale API. |
| `--api-url <URL>` | The base URL for the PlanetScale API. Default is `https://api.planetscale.com/`. |
| `--config <CONFIG_FILE>` | Config file. Default is `$HOME/.config/planetscale/pscale.yml`. |
| `--debug` | Enable debug mode. |
| `-f`, `--format <FORMAT>` | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. |
| `--no-color` | Disable color output. |
| `--service-token <TOKEN>` | The service token for authenticating. |
| `--service-token-id <TOKEN_ID>` | The service token ID for authenticating. |

## Examples

```shellscript
pscale pgbouncer list <DATABASE_NAME> <BRANCH_NAME> --org <ORGANIZATION_NAME>
pscale pgbouncer create <DATABASE_NAME> <BRANCH_NAME> --org <ORGANIZATION_NAME> --target replica --size PGB_10
pscale pgbouncer show <DATABASE_NAME> <BRANCH_NAME> <NAME> --org <ORGANIZATION_NAME>
pscale pgbouncer resize <DATABASE_NAME> <BRANCH_NAME> <NAME> --org <ORGANIZATION_NAME> --size PGB_20
pscale pgbouncer resize status <DATABASE_NAME> <BRANCH_NAME> <NAME> --org <ORGANIZATION_NAME>
pscale pgbouncer delete <DATABASE_NAME> <BRANCH_NAME> <NAME> --org <ORGANIZATION_NAME> --force
```

## Related documentation

## Dedicated PgBouncers

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
