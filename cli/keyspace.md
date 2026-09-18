---
url: https://planetscale.com/docs/cli/keyspace
title: "Keyspace"
description: ""
access_date: 2026-09-18T21:28:08.308Z
current_date: 2026-09-18T21:28:08.308Z
---

## Getting started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The keyspace command

This command allows you to view your keyspaces, create internal or [external keyspaces](#create-an-external-keyspace), resize them, and view or update your Vitess VSchemas. This command is not available for Postgres databases.

**Usage:**

```shellscript
pscale keyspace <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Product** | **Description** |
| --- | --- | --- | --- |
| `create <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` | `--cluster-size <SIZE>`, `--additional-replicas <NUMBER>`, `--shards <NUMBER>` | Vitess | Create a new keyspace within a database branch. |
| `create-external <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` | `--host <HOST>` \*, `--source-database <NAME>` \*, `--username <USER>` \*, `--password <PASSWORD>` \*, `--ssl-mode <MODE>` \*, `--port <PORT>`, `--cluster-size <SIZE>`, `--ssl-certificate-authority <CA>`, `--ssl-client-certificate <CERT>`, `--ssl-client-key <KEY>`, `--ssl-server-name <NAME>`, `--min-tls-version <VERSION>`, `--tablet-cell <CELL>`, `--skip-lint-errors`, `--dry-run`, `--wait` | Vitess | Create an external keyspace by connecting a production branch to an existing MySQL database. |
| `delete <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` | `--force` | Vitess | Delete a keyspace from a database branch. |
| `list <DATABASE_NAME> <BRANCH_NAME>` |  | Vitess | List all keyspaces within a database branch. |
| `show <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | Show a specific keyspace within a database branch. |
| `read-only-regions <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | List [read-only regions](../vitess/scaling/read-only-regions.md) for a keyspace. |
| `resize <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` | `--cluster-size <SIZE>`, `--additional-replicas <NUMBER>` | Vitess | Resize a keyspace, including external keyspaces. |
| `resize cancel <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | Cancel an ongoing keyspace resize. |
| `resize status <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | Show the status of the keyspace’s last resize. |
| `settings <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | Show the settings for a keyspace. |
| `update-settings <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` | `-i`, `--interactive`, `--max-rollout <NUMBER>`, `--replication-durability-constraints-strategy <STRATEGY>`, `--throttler-enabled`, `--throttler-threshold <SECONDS>`, `--vreplication-batch-replication-events`, `--vreplication-enable-noblob-binlog-mode`, `--vreplication-optimize-inserts` | Vitess | Update the settings for a keyspace. |
| `vschema show <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | Show the VSchema for a sharded keyspace. Empty on non-sharded keyspaces. |
| `vschema update <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` | `--vschema <FILE>` \* | Vitess | Update a VSchema of a keyspace. |
| `rollout-status <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>` |  | Vitess | Check the status of a keyspace resize request. |

> \* *Flag is required*

#### Sub-command flag descriptions

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--additional-replicas <NUMBER>` | `<NUMBER>` is the number of replicas to add to the keyspace. By default, production branches include 2 replicas. Do not use this flag for external keyspaces. | `create`, `resize` |
| `--cluster-size <SIZE>` | `<SIZE>` is the size of the database cluster. Optional for `create-external`; when omitted, PlanetScale chooses a size from the source storage. For `resize`, pass a size from `pscale size cluster list --external`. | `create`, `create-external`, `resize` |
| `--dry-run` | Check connectivity and schema compatibility without creating the keyspace. | `create-external` |
| `--host <HOST>` | Hostname of the external MySQL server. | `create-external` |
| `--min-tls-version <VERSION>` | Minimum TLS version for the external connection. | `create-external` |
| `--password <PASSWORD>` | Password for the external MySQL user. | `create-external` |
| `--port <PORT>` | Port of the external MySQL server. Default is `3306`. | `create-external` |
| `--skip-lint-errors` | Create even if datasource lint reports errors, when the organization allows it. | `create-external` |
| `--source-database <NAME>` | Name of the database on the external MySQL server, not the PlanetScale database. | `create-external` |
| `--ssl-certificate-authority <CA>` | CA certificate chain, or a path to a PEM file. | `create-external` |
| `--ssl-client-certificate <CERT>` | Client certificate, or a path to a PEM file. | `create-external` |
| `--ssl-client-key <KEY>` | Client private key, or a path to a PEM file. | `create-external` |
| `--ssl-mode <MODE>` | SSL verification mode: `disabled`, `preferred`, `required`, `verify_ca`, or `verify_identity`. | `create-external` |
| `--ssl-server-name <NAME>` | SSL server name override. | `create-external` |
| `--tablet-cell <CELL>` | Cell where the external tablet runs. | `create-external` |
| `--username <USER>` | Username to connect to the external MySQL server. | `create-external` |
| `--wait` | Wait until the keyspace is ready. | `create-external` |
| `--force` | Delete the keyspace without a confirmation prompt. Required in non-interactive or non- `human` output modes. | `delete` |
| `-i, --interactive` | Run the command in interactive mode. | `update-settings` |
| `--max-rollout <NUMBER>` | Maximum number of shards to update concurrently during a keyspace rollout. Accepts values from 1 to 32. | `update-settings` |
| `--replication-durability-constraints-strategy <STRATEGY>` | Replication strategy to use. Options: maximum, dynamic, minimum (default “maximum”). | `update-settings` |
| `--throttler-enabled` | Pause schema migrations and VReplication workflows when replication lag is above the threshold. Pass `--throttler-enabled=false` to turn the throttler off. | `update-settings` |
| `--throttler-threshold <SECONDS>` | Replication lag in seconds above which migrations and workflows are paused. Default is 5. | `update-settings` |
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

### Create an external keyspace

Attach an existing MySQL database as an external keyspace on a **production** branch. `--source-database` is the remote MySQL database name, not the PlanetScale database. `--cluster-size` is optional; if you omit it, PlanetScale chooses a size from the source storage.

```shellscript
pscale keyspace create-external <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME> \
  --host db.example.com \
  --source-database commerce \
  --username import \
  --password secret \
  --ssl-mode required \
  --wait
```

Check compatibility without creating the keyspace:

```shellscript
pscale keyspace create-external <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME> \
  --host db.example.com \
  --source-database commerce \
  --username import \
  --password secret \
  --ssl-mode required \
  --dry-run
```

`--dry-run` reports connection failures and schema lint errors. A source can still be created if it connects, even when lint reports table-level errors.

### Resize an external keyspace

Use the same `resize` command as an internal keyspace. Do not pass `--additional-replicas`.

```shellscript
pscale keyspace resize <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME> --cluster-size <SIZE>
pscale keyspace resize status <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>
```

You can also create and resize external keyspaces from the [Clusters](../vitess/cluster-configuration.md) page in the dashboard.

### Update tablet throttler settings

```shellscript
pscale keyspace settings <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>
pscale keyspace update-settings <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME> --throttler-enabled=false
pscale keyspace update-settings <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME> --throttler-threshold=10
```

### Update shard rollout concurrency

Set the maximum number of shards updated concurrently during keyspace configuration rollouts:

```shellscript
pscale keyspace settings <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>
pscale keyspace update-settings <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME> --max-rollout=4
```

`--max-rollout` accepts values from 1 to 32. If the API value is not set, `pscale keyspace settings` displays `not set`, and PlanetScale uses the default of 1.

### List read-only regions for a keyspace

```shellscript
pscale keyspace read-only-regions <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>
```

Output includes each region’s slug, cluster size, and replica count. Use a region slug with `pscale password create ... --read-only-region` or `pscale database dump ... --read-only-region`.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
