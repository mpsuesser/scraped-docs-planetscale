---
url: https://planetscale.com/docs/cli/keyspace
title: "Keyspace"
description: ""
access_date: 2026-08-05T19:12:39.041Z
current_date: 2026-08-05T19:12:39.041Z
---

## Getting started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The keyspace command

This command allows you to view your keyspaces and view or update your Vitess VSchemas. This command is not available for Postgres databases.

**Usage:**

```shellscript
pscale keyspace <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Product** | **Description** |
| --- | --- | --- | --- |
| `create <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` | `--cluster-size <SIZE>`, `--additional-replicas <NUMBER>`, `--shards <NUMBER>` | Vitess | Create a new keyspace within a database branch. |
| `list <DATABASE_NAME> <BRANCH_NAME>` |  | Vitess | List all keyspaces within a database branch. |
| `show <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | Show a specific keyspace within a database branch. |
| `read-only-regions <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | List [read-only regions](../vitess/scaling/read-only-regions.md) for a keyspace. |
| `resize <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` | `--cluster-size <SIZE>`, `--additional-replicas <NUMBER>` | Vitess | Resize a keyspace. |
| `resize cancel <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | Cancel an ongoing keyspace resize. |
| `resize status <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | Show the status of the keyspace’s last resize. |
| `settings <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | Show the settings for a keyspace. |
| `update-settings <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` | `-i`, `--interactive`, `--replication-durability-constraints-strategy <STRATEGY>`, `--vreplication-batch-replication-events`, `--vreplication-enable-noblob-binlog-mode`, `--vreplication-optimize-inserts` | Vitess | Update the settings for a keyspace. |
| `vschema show <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | Show the VSchema for a sharded keyspace. Empty on non-sharded keyspaces. |
| `vschema update <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` | `--vschema <FILE>` \* | Vitess | Update a VSchema of a keyspace. |
| `rollout-status <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | Check the status of a keyspace resize request. |

> \* *Flag is required*

#### Sub-command flag descriptions

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--additional-replicas <NUMBER>` | `<NUMBER>` is the number of replicas to add to the keyspace. By default, production branches include 2 replicas. | `create`, `resize` |
| `--cluster-size <SIZE>` | `<SIZE>` is the size of the database cluster. | `create`, `resize` |
| `-i, --interactive` | Run the command in interactive mode. | `update-settings` |
| `--replication-durability-constraints-strategy <STRATEGY>` | Replication strategy to use. Options: maximum, dynamic, minimum (default “maximum”). | `update-settings` |
| `--shards <NUMBER>` | Number of shards in the keyspace (default 1). | `create` |
| `--vreplication-batch-replication-events` | When enabled, sends fewer queries to MySQL to improve performance. | `update-settings` |
| `--vreplication-enable-noblob-binlog-mode` | When enabled, omits changed BLOB and TEXT columns from replication events, which reduces binlog sizes. (default true) | `update-settings` |
| `--vreplication-optimize-inserts` | When enabled, skips sending INSERT events for rows that have yet to be replicated. (default true) | `update-settings` |
| `--vschema <FILE>` | `<FILE>` is the path to the updated VSchema file. | `vschema update` |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for auth command |
| `--org <ORGANIZATION_NAME>` | The organization for the current user |

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

### List read-only regions for a keyspace

```shellscript
pscale keyspace read-only-regions <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>
```

Output includes each region’s slug, cluster size, and replica count. Use a region slug with `pscale password create ... --read-only-region` or `pscale database dump ... --read-only-region`.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
