---
url: https://planetscale.com/docs/cli/vtctl
title: "Vtctl"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The vtctl command

Run Vitess `vtctld` operations against a Vitess database branch. Commands mirror Vitess [`vtctldclient`](https://vitess.io/docs/reference/programs/vtctldclient/) where possible.

**Usage:**

```shellscript
pscale branch vtctl <SUB-COMMAND> <DATABASE_NAME> <BRANCH_NAME>
```

`pscale branch vtctld` is an alias for `pscale branch vtctl`.

Most subcommands print JSON. Pass `--format json` when you script them so progress messages don’t mix with the JSON.

### Available sub-commands

| **Sub-command** | **Description** |
| --- | --- |
| [`move-tables`](move-tables.md) | Copy tables between keyspaces and import from an external keyspace |
| [`materialize`](materialize.md) | Create near-realtime materialized tables, aggregations, or views |
| [`lookup-vindex`](lookup-vindex.md) | Create and backfill a Lookup Vindex for an existing table |
| [`vdiff`](vdiff.md) | Compare source and target tables for a VReplication workflow |
| [`planned-reparent-shard`](planned-reparent-shard.md) | Promote a healthy replica to primary when both tablets are up |
| `list-tablets` | List tablets for a branch, grouped by keyspace and shard |
| `list-workflows` | List VReplication workflows in a keyspace |
| `list-keyspaces` | List keyspaces from the live cluster |
| `start-workflow` / `stop-workflow` | Start or stop a named workflow in a keyspace |
| `get-shard` | Read a live shard record, including tablet controls and denied tables |
| `set-shard-tablet-control` | Update shard tablet controls (emergency / post-MoveTables cleanup) |
| `refresh-state-by-shard` | Reload tablet records for a shard |
| `get-routing-rules` | Read live table routing rules from the cluster |
| `get-keyspace-routing-rules` / `apply-keyspace-routing-rules` | Read or replace live keyspace routing rules |
| `throttler` | Inspect and configure the tablet throttler |

### Flags shared by many sub-commands

| **Flag** | **Description** |
| --- | --- |
| `--org <ORGANIZATION_NAME>` | The organization for the current user |
| `-h`, `--help` | View help for `vtctl` or a sub-command |

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

## List tablets

Discover tablet aliases:

```shellscript
pscale branch vtctl list-tablets <DATABASE_NAME> <BRANCH_NAME> --org <ORG> --format json
pscale branch vtctl list-tablets <DATABASE_NAME> <BRANCH_NAME> --org <ORG> --keyspace <KEYSPACE_NAME> --shard '-' --format json
```

| **Flag** | **Description** |
| --- | --- |
| `--keyspace <NAME>` | Only list tablets in this keyspace |
| `--shard <SHARD>` | Only list tablets in this shard (requires `--keyspace`) |
| `--tablet-type <TYPE>` | Only list tablets of this type (`primary`, `replica`, `rdonly`) |
| `--tablet-alias <ALIAS>` | Only list these tablet aliases (comma-separated; overrides other filters) |

Matching API: `GET /v1/organizations/:org/databases/:db/branches/:branch/tablets`.

## Shard tablet controls

Use `get-shard` to inspect tablet controls and denied tables. Use `set-shard-tablet-control` only for an emergency fix or after a finished [MoveTables](move-tables.md) workflow — the same guidance as Vitess [`SetShardTabletControl`](https://vitess.io/docs/reference/programs/vtctldclient/vtctldclient_setshardtabletcontrol/).

[Service tokens](../api/service-tokens.md) need `write_branch_topology` for development branches and `write_production_branch_topology` for production branches to update shard tablet controls. The same accesses apply to [PlannedReparentShard](planned-reparent-shard.md).

```shellscript
pscale branch vtctl get-shard <DATABASE_NAME> <BRANCH_NAME> \
  --org <ORG> \
  --keyspace <KEYSPACE_NAME> \
  --shard '-' \
  --format json

pscale branch vtctl set-shard-tablet-control <DATABASE_NAME> <BRANCH_NAME> \
  --org <ORG> \
  --keyspace <KEYSPACE_NAME> \
  --shard '-' \
  --tablet-type replica \
  --denied-tables <TABLE_NAMES> \
  --format json
```

| **Flag** | **Description** |
| --- | --- |
| `--keyspace <NAME>` \* | Keyspace name |
| `--shard <SHARD>` \* | Shard name (`-` for unsharded) |
| `--tablet-type <TYPE>` \* | Tablet type (`rdonly`, `replica`, `primary`) |
| `--cells <CELLS>` | Cells to update (comma-separated) |
| `--denied-tables <TABLES>` | Tables to add to or remove from the denylist |
| `--remove` | Remove tablet controls, or remove specific denied tables when combined with `--denied-tables` |
| `--disable-query-service` | Disable query service on the provided tablets |

> \* *Flag is required*

You must pass at least one of `--remove`, `--denied-tables`, or `--disable-query-service`.

Matching APIs: `GET .../vtctld/shard`, `PUT .../vtctld/shard/tablet-control`, `POST .../vtctld/shard/refresh-state`.

## Dashboard

The **Workflows** page lists MoveTables and Materialize workflows and is read-only. Create and drive them with the CLI or API. See [Workflows](../vitess/scaling/workflows.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
