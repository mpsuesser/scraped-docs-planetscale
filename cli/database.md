---
url: https://planetscale.com/docs/cli/database
title: "Database"
description: ""
access_date: 2026-08-17T22:26:09.446Z
current_date: 2026-08-17T22:26:09.446Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The database command

This command allows you to create, read, update, delete, dump, and restore databases, manage Postgres IP restrictions, and configure the Vitess database-level migration throttler.

**Usage:**

```shellscript
pscale database <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Description** | **Product** |
| --- | --- | --- | --- |
| `create <DATABASE_NAME>` | `--region <REGION_NAME>`, `--plan <PLAN>`, `--cluster_size <CLUSTER_SIZE>`, `--major-version <MAJOR_VERSION>`, `--min-storage <BYTES>`, `--max-storage <BYTES>` | Create a database with the specified name | Postgres, Vitess |
| `delete <DATABASE_NAME>` | `--force` | Delete the specified database | Postgres, Vitess |
| `dump <DATABASE_NAME> <BRANCH_NAME>` | `--local-addr <ADDRESS>`, `--output <DIRECTORY_NAME>`, `--tables <TABLES_LIST>`, `--columns <TABLE:COLUMN_LIST>`, `--threads <NUMBER_OF_THREADS> (defaults to 16)` | Backup and dump the specified database | Vitess |
| `ip-restriction list <DATABASE_NAME>` |  | List IP restriction entries | Postgres |
| `ip-restriction show <DATABASE_NAME> <ENTRY_ID>` |  | Show an IP restriction entry | Postgres |
| `ip-restriction create <DATABASE_NAME>` | `--cidrs <CIDR>` \*, `--schema <SCHEMA>`, `--role <ROLE>`, `--description <TEXT>` | Create an IP restriction entry | Postgres |
| `ip-restriction update <DATABASE_NAME> <ENTRY_ID>` | `--cidrs <CIDR>`, `--schema <SCHEMA>`, `--role <ROLE>`, `--description <TEXT>` | Update an IP restriction entry | Postgres |
| `ip-restriction delete <DATABASE_NAME> <ENTRY_ID>` | `--force` | Delete an IP restriction entry | Postgres |
| `list <DATABASE_NAME>` |  | List all databases in the current org | Postgres, Vitess |
| `restore-dump <DATABASE_NAME> <BRANCH_NAME>` | `--dir <DIRECTORY_NAME>` \*, `--local-addr <ADDRESS>`, `--overwrite-tables`, `--threads <NUMBER_OF_THREADS> (defaults to 1)`, `--allow-different-destination`, `--show-details`, `--schema-only`, `--data-only`, `--starting-table <STARTING_TABLE>`, `--ending-table <ENDING_TABLE>` | Restore the specified database from a local dump directory | Postgres, Vitess |
| `show <DATABASE_NAME>` | `--web` | Retrieve information about a database | Postgres, Vitess |
| `throttler show <DATABASE_NAME>` |  | Show database-level Vitess migration throttler configuration | Vitess |
| `throttler update <DATABASE_NAME>` | `--ratio <RATIO>`, `--configuration <KEYSPACE=RATIO>` | Update database-level Vitess migration throttler configuration | Vitess |
| `update <DATABASE_NAME>` | `--new-name`, `--default-branch`, `--restrict-branch-region`, `--production-branch-web-console`, `--insights-raw-queries`, `--require-approval-for-deploy`, `--allow-data-branching`, `--allow-foreign-key-constraints`, `--automatic-migrations`, `--migration-framework`, `--migration-table-name` | Update database settings | Postgres, Vitess |

### Service token automation: database

Legend: ✅ supported · 🚫 unavailable · 👤 interactive login only. All sub-commands require `--org` or `PLANETSCALE_ORG`.

| Sub-command | Env-var auth | `--service-token` flag | Requires `--org` | Postgres / Vitess | `--format json` | API equivalent |
| --- | --- | --- | --- | --- | --- | --- |
| `list` | ✅ | ✅ | ✅ | Both | ✅ | `pscale api organizations/<org>/databases --format json` |
| `show <database>` | ✅ | ✅ | ✅ | Both | ✅ | `pscale api organizations/<org>/databases/<database> --format json` |
| `create <database>` | ✅ | ✅ | ✅ | Both | ✅ | `POST pscale api organizations/<org>/databases --format json` |
| `delete <database>` | ✅ | ✅ | ✅ | Both | ✅ | `DELETE pscale api organizations/<org>/databases/<database> --format json` |
| `dump` | ✅ | ✅ | ✅ | Vitess | N/A | N/A |
| `restore-dump` | ✅ | ✅ | ✅ | Both | N/A | N/A |

```shellscript
export PLANETSCALE_ORG="<org>"
pscale database list --format json
pscale database show <database> --format json
```

> \* *Flag is required*

#### Sub-command flag descriptions

Some of the sub-commands have additional flags unique to the sub-command. This section covers what each of those does. See the above table for which context.

| **Sub-command flag** | **Description** | **Applicable sub-commands** |
| --- | --- | --- |
| `--region` | Specify the [region](https://planetscale.com/docs/vitess/regions) of the new database. Default is `us-east`. | `create` |
| `--plan` | Specify the plan for the database. Currently, `scaler_pro` is the only option and the default. | `create` |
| `--cluster_size` | For Base plan databases, you may specify the cluster size. Default is `PS_10` | `create` |
| `--major-version` | The major version of the database (Postgres only). Currently supports `17` or `18`. | `create` |
| `--min-storage` | Minimum storage size in bytes for Postgres databases using Amazon Elastic Block Storage (EBS). | `create` |
| `--max-storage` | Maximum storage size in bytes for Postgres databases using Amazon Elastic Block Storage (EBS). | `create` |
| `--force` | Skip confirmation for destructive actions. | `delete`, `ip-restriction delete` |
| `--cidrs <CIDR>` | IPv4 CIDR ranges to allow. Repeatable or comma-separated. Required on create; replaces the existing list on update. | `ip-restriction create`, `ip-restriction update` |
| `--schema <SCHEMA>` | Postgres schema to restrict. Omit (or empty on update) for all schemas. | `ip-restriction create`, `ip-restriction update` |
| `--role <ROLE>` | Postgres role to restrict. Omit (or empty on update) for all roles. | `ip-restriction create`, `ip-restriction update` |
| `--description <TEXT>` | Optional description for the IP restriction entry. | `ip-restriction create`, `ip-restriction update` |
| `--new-name <NAME>` | Rename the database. | `update` |
| `--default-branch <BRANCH>` | Set the default branch. | `update` |
| `--restrict-branch-region` | Limit branch creation to the database region. Boolean; set explicitly with `=true` or `=false`. | `update` |
| `--production-branch-web-console` | Allow the web console on the production branch. Boolean; set explicitly with `=true` or `=false`. | `update` |
| `--insights-raw-queries` | Collect full SQL for Insights (Vitess only). Boolean; set explicitly with `=true` or `=false`. | `update` |
| `--require-approval-for-deploy` | Require admin approval for deploy requests (Vitess only). Boolean; set explicitly with `=true` or `=false`. | `update` |
| `--allow-data-branching` | Allow seeding branches with data (Vitess only). Boolean; set explicitly with `=true` or `=false`. | `update` |
| `--allow-foreign-key-constraints` | Allow foreign key constraints (Vitess only). Boolean; set explicitly with `=true` or `=false`. | `update` |
| `--automatic-migrations` | Copy migration data to new branches and deploy requests (Vitess only). Boolean; set explicitly with `=true` or `=false`. | `update` |
| `--migration-framework <NAME>` | Migration framework for the database (Vitess only). | `update` |
| `--migration-table-name <NAME>` | Migration table name for the database (Vitess only). | `update` |
| `--ratio <RATIO>` | Throttler ratio between 0 and 95 applied to all eligible keyspaces. 0 disables throttling; 95 slows migrations the most. | `throttler update` |
| `--configuration <KEYSPACE=RATIO>` | Per-keyspace throttler ratio as `keyspace=ratio` (repeatable). Use instead of `--ratio`, not together. | `throttler update` |
| `--local-addr <ADDRESS>` | Local address to bind and listen for connections. By default the proxy binds to 127.0.0.1 with a random port. | `dump`, `restore-dump` |
| `--threads` | Number of concurrent threads to use | `dump`, `restore-dump` |
| `--output <DIRECTORY_NAME>` | Output directory of the dump. By default the dump is saved to a folder in the current directory. | `dump` |
| `--tables <TABLES_LIST>` | Comma separated string of tables to dump. By default, all tables are dumped. | `dump` |
| `--columns <TABLE:COLUMN_LIST>` | Specify which columns to include when dumping specific tables. Use format `table:col1,col2`. Can be specified multiple times. | `dump` |
| `--wheres string` | Comma separated string of WHERE clauses to filter the tables to dump. | `dump` |
| `--replica` | Dump from a replica tablet in the primary region (if available; will fail if not). | `dump` |
| `--rdonly` | Dump from a rdonly tablet in the primary region (if available; will fail if not). Not for separate read-only regions. Use `--read-only-region` instead. | `dump` |
| `--read-only-region <REGION>` | Dump from a Vitess [read-only region](../vitess/scaling/read-only-regions.md) (region slug, display name, or id). List regions with `pscale keyspace read-only-regions`. Cannot be combined with `--replica` or `--rdonly`. | `dump` |
| `--keyspace <KEYSPACE_NAME>` | Optionally target a specific keyspace to be dumped. Useful for sharded databases. | `dump` |
| `--shard <SHARD_NAME>` | Optional shard to target, must be used with keyspace | `dump` |
| `--output-format <FORMAT>` | Output format for the dump. Options: `sql` (default), `json`, `csv`. | `dump` |
| `--dir <DIRECTORY_NAME>` | Directory containing the files to be used for the restore. | `restore-dump` |
| `--overwrite-tables` | If true, will attempt to DROP TABLE before restoring. | `restore-dump` |
| `--allow-different-destination` | If true, will allow you to restore the files to a database with a different name without needing to rename the existing dump’s files. | `restore-dump` |
| `--show-details` | If true, will add extra output during the restore process. | `restore-dump` |
| `--schema-only` | If true, will only restore the schema files during the restore process. | `restore-dump` |
| `--data-only` | If true, will only restore the data files during the restore process. | `restore-dump` |
| `--starting-table <TABLE_NAME>` | Table to start from for the restore (useful for restarting from a certain point) | `restore-dump` |
| `--ending-table <TABLE_NAME>` | Table to end at for the restore (useful for stopping restore at a certain point) | `restore-dump` |
| `--web` | Perform the action in your web browser | `show` |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | Get help with the `database` command |
| `--org <ORGANIZATION_NAME>` | Specify the organization for the database you’re acting upon |

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

The `--format` flag does not apply to the database dump files created by the `dump` subcommand. However, you can control the output format using the `--output-format` flag, which supports SQL (default), JSON, and CSV formats. When using SQL format, dumps are compatible with [mydumper](https://github.com/mydumper/mydumper).

## Examples

### Create a new scaler\_pro database

**Command:**

```shellscript
pscale database create new-database --region <REGION_NAME> --plan scaler_pro --cluster_size PS_80
```

**Output:**

Database `new-database` was successfully created.

### Create a Postgres database with storage size settings (Amazon EBS)

Use `--min-storage` and `--max-storage` to set the initial and maximum storage size in bytes when creating a Postgres database on Amazon Elastic Block Storage (EBS).

**Command:**

```shellscript
pscale database create new-postgres-db --engine postgres --region <REGION_NAME> --min-storage 10737418240 --max-storage 21474836480
```

In this example, the database starts at 10 GiB (`10737418240` bytes) and can scale up to 20 GiB (`21474836480` bytes).

### Create a dump of an existing branch:

This command is only available for Vitess databases. For Postgres databases, use the [pg\_dump](https://www.postgresql.org/docs/current/app-pgdump.html) command instead.

**Command:**

```shellscript
pscale database dump <CURRENT_DATABASE_NAME> <BRANCH_NAME> --output="<DIRECTORY_FOR_BACKUP>" --org="<ORIGINAL_ORGANIZATION>"
```

**Output:**

A local export of your database will be generated within the current directory by default but since we are providing an `--output` location above that will be used instead.

### Export data in different formats:

You can specify the output format when dumping your Vitess database using the `--output-format` flag:

**Export as JSON:**

```shellscript
pscale database dump <DATABASE_NAME> <BRANCH_NAME> --output-format=json
```

**Export as CSV:**

```shellscript
pscale database dump <DATABASE_NAME> <BRANCH_NAME> --output-format=csv
```

**Export as SQL (default):**

```shellscript
pscale database dump <DATABASE_NAME> <BRANCH_NAME> --output-format=sql
```

### Dump specific columns from tables:

You can use the `--columns` flag to export only specific columns from your tables. This is useful when you need a partial export or want to exclude sensitive data:

```shellscript
# Export only specific columns from multiple tables
pscale database dump <DATABASE_NAME> <BRANCH_NAME> --columns 'users:id,name' --columns 'orders:id,total'
```

The schema dump will include the complete table schema, but the data files will only contain the specified columns.

### Dump from a read-only region:

For Vitess databases with [read-only regions](../vitess/scaling/read-only-regions.md), dump from a specific region instead of the primary:

```shellscript
pscale database dump <DATABASE_NAME> <BRANCH_NAME> --read-only-region eu-west
```

Pass a region slug, display name, or id. List configured regions with `pscale keyspace read-only-regions <DATABASE_NAME> <BRANCH_NAME> <KEYSPACE_NAME>`.

### Update database settings

Only flags you pass are sent to the API. Boolean flags must be set explicitly (`=true` or `=false`). Flags marked Vitess-only are rejected for PostgreSQL databases.

```shellscript
pscale database update <DATABASE_NAME> --default-branch main --restrict-branch-region=true
pscale database update <DATABASE_NAME> --require-approval-for-deploy=true
```

### Manage Postgres IP restrictions

Aliases: `pscale database cidr` and `pscale database cidrs`. Postgres only.

```shellscript
pscale database ip-restriction list <DATABASE_NAME>
pscale database ip-restriction create <DATABASE_NAME> --cidrs 203.0.113.0/24 --description office
pscale database ip-restriction update <DATABASE_NAME> <ENTRY_ID> --cidrs 203.0.113.0/24,198.51.100.10/32
pscale database ip-restriction delete <DATABASE_NAME> <ENTRY_ID>
```

### Database-level Vitess migration throttler

Sets the default throttler for future deploy requests on the database. This is not the [per-deploy-request throttler](deploy-request.md#the-deploy-request-command-with-throttler-update-subcommand) and not the tablet/vtctld throttler. Vitess only. Use `--ratio` for one ratio across all eligible keyspaces, or `--configuration` for per-keyspace ratios (not both).

```shellscript
pscale database throttler show <DATABASE_NAME>
pscale database throttler update <DATABASE_NAME> --ratio 25
pscale database throttler update <DATABASE_NAME> --configuration main=10 --configuration sharded=40
```

### Restore a backup to an existing branch:

**Command:**

```shellscript
pscale database restore-dump <DESTINATION_DATABASE_NAME> <BRANCH_NAME> --dir="<DIRECTORY_FOR_BACKUP>" --org="<DESTINATION_ORGANIZATION>"
```

**Output:**

You should receive output indicating the restore is progressing until it completes successfully.

The command above will allow you to restore an existing backup to another branch located either within the same organization/database as the original, or within a completely different organization/database.

As of `pscale` v0.218.0 or newer the `--allow-different-destination` flag is now available. If this flag is provided it will make the steps below about renaming the files unnecessary.

If you opt to import into a database with a different name you will have to make sure you rename the files from your backup beforehand.

For example, the files will be named something like this:

```shellscript
<CURRENT_DATABASE_NAME>.<TABLE_NAME>-schema.sql
```

And you will want to rename all of the files in the dump folder to have the new database name if it is not the same as the existing one:

```shellscript
<DESTINATION_DATABASE_NAME>.<TABLE_NAME>-schema.sql
```

If importing into a branch that already contains table definitions that you want to overwrite, you may also be required to pass in the optional `--overwrite-tables` flag.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
