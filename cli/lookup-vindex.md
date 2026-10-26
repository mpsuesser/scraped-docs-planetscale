---
url: https://planetscale.com/docs/cli/lookup-vindex
title: "Lookup Vindex"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The lookup-vindex command

Run [Vitess LookupVindex](https://vitess.io/docs/reference/vreplication/lookupvindex/) on a Vitess database branch. Use it to create and backfill a [Lookup Vindex](../vitess/sharding/vindexes.md#lookup-vindex) for a table that already has data.

`create` starts a VReplication backfill. Until you **externalize**, Vitess keeps the lookup table in sync with VReplication. After you externalize an owned Lookup Vindex, writes to the owner table update the lookup table in the same transaction. See the Vitess [Creating a LookupVindex](https://vitess.io/docs/user-guides/configuration-advanced/createlookupvindex/) guide for background.

**Usage:**

```shellscript
pscale branch vtctl lookup-vindex <SUB-COMMAND> <DATABASE_NAME> <BRANCH_NAME>
```

`pscale branch vtctld lookup-vindex` also works.

You must be an [Organization Administrator](../security/access-control.md#organization-administrator) or a [Database Administrator](../security/access-control.md#database-administrator) of the database to create and change Lookup Vindexes. [Service tokens](../api/service-tokens.md) need `read_workflow` to show a Lookup Vindex, `write_workflow` to create, externalize, internalize, or complete one, and `delete_workflow` to cancel one.

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Description** |
| --- | --- | --- |
| `create <DATABASE_NAME> <BRANCH_NAME>` | `--name <NAME>` \*, `--table-keyspace <KEYSPACE_NAME>` \*, `--keyspace <KEYSPACE_NAME>`, `--type <TYPE>`, `--table-owner <TABLE>`, `--table-name <TABLE>`, `--table-owner-columns <COLUMNS>`, `--table-vindex-type <TYPE>`, `--cells <CELLS>`, `--tablet-types <TYPES>`, `--ignore-nulls`, `--tablet-types-in-preference-order`, `--continue-after-copy-with-owner` | Create and start backfilling a Lookup Vindex |
| `show <DATABASE_NAME> <BRANCH_NAME>` | `--name <NAME>` \*, `--table-keyspace <KEYSPACE_NAME>` \* | Show Lookup Vindex details |
| `externalize <DATABASE_NAME> <BRANCH_NAME>` | `--name <NAME>` \*, `--table-keyspace <KEYSPACE_NAME>` \*, `--keyspace <KEYSPACE_NAME>`, `--delete` | Make the Lookup Vindex usable for queries |
| `internalize <DATABASE_NAME> <BRANCH_NAME>` | `--name <NAME>` \*, `--table-keyspace <KEYSPACE_NAME>` \*, `--keyspace <KEYSPACE_NAME>` | Reverse externalization so backfill can resume |
| `cancel <DATABASE_NAME> <BRANCH_NAME>` | `--name <NAME>` \*, `--table-keyspace <KEYSPACE_NAME>` \* | Cancel Lookup Vindex creation and its VReplication workflow |
| `complete <DATABASE_NAME> <BRANCH_NAME>` | `--name <NAME>` \*, `--table-keyspace <KEYSPACE_NAME>` \*, `--keyspace <KEYSPACE_NAME>` | Finish after externalization |

> \* *Flag is required*

#### Sub-command flag descriptions

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--name <NAME>` | Lookup Vindex name | all |
| `--table-keyspace <KEYSPACE_NAME>` | Keyspace where the lookup table and its backfill workflow are created | all |
| `--keyspace <KEYSPACE_NAME>` | Keyspace of the owner table, where the Lookup Vindex is defined | `create`, `externalize`, `internalize`, `complete` |
| `--type <TYPE>` | Lookup Vindex type (for example `consistent_lookup_unique`) | `create` |
| `--table-owner <TABLE>` | Owner table name | `create` |
| `--table-name <TABLE>` | Name of the lookup table | `create` |
| `--table-owner-columns <COLUMNS>` | Owner table columns (comma-separated) | `create` |
| `--table-vindex-type <TYPE>` | Vindex type on the owner table | `create` |
| `--cells <CELLS>` | Cells to replicate from (comma-separated) | `create` |
| `--tablet-types <TYPES>` | Tablet types to replicate from (comma-separated) | `create` |
| `--ignore-nulls` | Ignore null values | `create` |
| `--tablet-types-in-preference-order` | Use tablet types in preference order | `create` |
| `--continue-after-copy-with-owner` | Keep the backfill running after copy when the vindex has an owner (default `true`) | `create` |
| `--delete` | Delete the workflow after externalizing | `externalize` |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for `lookup-vindex` |
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

## Lifecycle

1. **Create** the Lookup Vindex and wait for the backfill to finish (`show`).
2. **Externalize** so Vitess can use it for routing.
3. **Complete** after externalization, or **internalize** if you need to resume backfill.
4. **Cancel** to abandon creation before you finish.

## Examples

```shellscript
pscale branch vtctl lookup-vindex create <DATABASE_NAME> <BRANCH_NAME> \
  --org <ORG> \
  --name <VINDEX_NAME> \
  --table-keyspace <LOOKUP_KEYSPACE> \
  --keyspace <OWNER_KEYSPACE> \
  --type consistent_lookup_unique \
  --table-owner <OWNER_TABLE> \
  --table-name <LOOKUP_TABLE> \
  --table-owner-columns <COLUMNS> \
  --format json

pscale branch vtctl lookup-vindex show <DATABASE_NAME> <BRANCH_NAME> \
  --org <ORG> \
  --name <VINDEX_NAME> \
  --table-keyspace <LOOKUP_KEYSPACE> \
  --format json

pscale branch vtctl lookup-vindex externalize <DATABASE_NAME> <BRANCH_NAME> \
  --org <ORG> \
  --name <VINDEX_NAME> \
  --table-keyspace <LOOKUP_KEYSPACE> \
  --keyspace <OWNER_KEYSPACE> \
  --format json
```

## API

Matching API paths under `/v1/organizations/:org/databases/:db/branches/:branch/lookup-vindex/vindexes`.

For VSchema concepts, see [Vindexes](../vitess/sharding/vindexes.md) and [Alter VSchema](../vitess/sharding/alter-vschema.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
