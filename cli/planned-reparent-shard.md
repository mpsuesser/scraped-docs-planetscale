---
url: https://planetscale.com/docs/cli/planned-reparent-shard
title: "Planned Reparent Shard"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The planned-reparent-shard command

Run [Vitess PlannedReparentShard](https://vitess.io/docs/user-guides/configuration-advanced/reparenting/#plannedreparentshard-planned-reparenting) on a Vitess database branch. It reparents a healthy shard to a new primary when **both** the current and new primaries are up and running.

Use this for planned maintenance when you want to choose the new primary.

Discover tablet aliases with [`pscale branch vtctl list-tablets`](vtctl.md#list-tablets) before you reparent.

**Usage:**

```shellscript
pscale branch vtctl planned-reparent-shard <DATABASE_NAME> <BRANCH_NAME>
pscale branch vtctl planned-reparent-shard status <DATABASE_NAME> <BRANCH_NAME> <OPERATION_ID>
```

`pscale branch vtctld planned-reparent-shard` also works.

PlannedReparentShard briefly interrupts writes on the shard while the primary moves. In-flight transactions may fail; clients should retry. Prefer a quiet window and confirm replica lag is low before you start.

You must be an [Organization Administrator](../security/access-control.md#organization-administrator) or a [Database Administrator](../security/access-control.md#database-administrator) of the database. [Service tokens](../api/service-tokens.md) need `write_branch_topology` for development branches and `write_production_branch_topology` for production branches.

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Description** |
| --- | --- | --- |
| `<DATABASE_NAME> <BRANCH_NAME>` | `--keyspace <KEYSPACE_NAME>` \*, `--shard <SHARD>` \*, `--new-primary <TABLET_ALIAS>` \*, `--wait` | Start a PlannedReparentShard operation |
| `status <DATABASE_NAME> <BRANCH_NAME> <OPERATION_ID>` |  | Show the status of a PlannedReparentShard operation |

> \* *Flag is required*

#### Sub-command flag descriptions

| **Sub-command flag** | **Description** |
| --- | --- |
| `--keyspace <KEYSPACE_NAME>` | Keyspace name |
| `--shard <SHARD>` | Shard range (for example `-80`, `80-`, or `-` for unsharded) |
| `--new-primary <TABLET_ALIAS>` | Tablet alias to promote (for example `zone1-0000000101`) |
| `--wait` | Wait for the operation to finish (default `true`). Pass `--wait=false` to return the operation id immediately |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `planned-reparent-shard` |
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

List tablets, then reparent a shard:

```shellscript
pscale branch vtctl list-tablets <DATABASE_NAME> <BRANCH_NAME> \
  --org <ORG> \
  --keyspace <KEYSPACE_NAME> \
  --shard '-' \
  --format json

pscale branch vtctl planned-reparent-shard <DATABASE_NAME> <BRANCH_NAME> \
  --org <ORG> \
  --keyspace <KEYSPACE_NAME> \
  --shard '-' \
  --new-primary <TABLET_ALIAS> \
  --format json
```

By default the command waits for the async operation to finish. Pass `--wait=false` to get an operation id immediately, then poll with the PlanetScale `status` subcommand (this is not a Vitess `vtctldclient` command — it reads `GET .../planned-reparent/:id`):

```shellscript
pscale branch vtctl planned-reparent-shard <DATABASE_NAME> <BRANCH_NAME> \
  --org <ORG> \
  --keyspace <KEYSPACE_NAME> \
  --shard '-' \
  --new-primary <TABLET_ALIAS> \
  --wait=false \
  --format json

pscale branch vtctl planned-reparent-shard status <DATABASE_NAME> <BRANCH_NAME> <OPERATION_ID> \
  --org <ORG> \
  --format json
```

## API

- `POST /v1/organizations/:org/databases/:db/branches/:branch/planned-reparent`
- `GET /v1/organizations/:org/databases/:db/branches/:branch/planned-reparent/:id`

Body fields for create: `keyspace`, `shard`, `new_primary`. The create response is a vtctld operation; poll with the show endpoint or `status` until it completes.

There is no customer-facing EmergencyReparentShard command. PlanetScale handles emergency recovery automatically.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
