---
url: https://planetscale.com/docs/cli/database
title: "Database"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale CLI commands: database

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

<PlatformAvailability current="both" />

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you've installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The `database` command

This command allows you to create, read, delete, dump, and restore databases.

**Usage:**

```bash theme={null}
pscale database <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command**                              | **Sub-command flags**                                                                                                                                                                                                                                                                | **Description**                                            | **Product**      |
| :------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------- | :--------------- |
| `create <DATABASE_NAME>`                     | `--region <REGION_NAME>`, `--plan <PLAN>`, `--cluster_size <CLUSTER_SIZE>`, `--major-version <MAJOR_VERSION>`, `--min-storage <BYTES>`, `--max-storage <BYTES>`                                                                                                                      | Create a database with the specified name                  | Postgres, Vitess |
| `delete <DATABASE_NAME>`                     | `--force`                                                                                                                                                                                                                                                                            | Delete the specified database                              | Postgres, Vitess |
| `dump <DATABASE_NAME> <BRANCH_NAME>`         | `--local-addr <ADDRESS>`, `--output <DIRECTORY_NAME>`, `--tables <TABLES_LIST>`, `--columns <TABLE:COLUMN_LIST>`, `--threads <NUMBER_OF_THREADS> (defaults to 16)`                                                                                                                   | Backup and dump the specified database                     | Vitess           |
| `list <DATABASE_NAME>`                       |                                                                                                                                                                                                                                                                                      | List all databases in the current org                      | Postgres, Vitess |
| `restore-dump <DATABASE_NAME> <BRANCH_NAME>` | `--dir <DIRECTORY_NAME>`\*, `--local-addr <ADDRESS>`, `--overwrite-tables`, `--threads <NUMBER_OF_THREADS> (defaults to 1)`, `--allow-different-destination`, `--show-details`, `--schema-only`, `--data-only`, `--starting-table <STARTING_TABLE>`, `--ending-table <ENDING_TABLE>` | Restore the specified database from a local dump directory | Postgres, Vitess |
| `show <DATABASE_NAME>`                       | `--web`                                                                                                                                                                                                                                                                              | Retrieve information about a database                      | Postgres, Vitess |

### Service token automation: `database`

Legend: ✅ supported · 🚫 unavailable · 👤 interactive login only. All sub-commands require `--org` or `PLANETSCALE_ORG`.

| Sub-command         | Env-var auth | `--service-token` flag | Requires `--org` | Postgres / Vitess | `--format json` | API equivalent                                                             |
| :------------------ | :----------: | :--------------------: | :--------------: | :---------------- | :-------------: | :------------------------------------------------------------------------- |
| `list`              |       ✅      |            ✅           |         ✅        | Both              |        ✅        | `pscale api organizations/<org>/databases --format json`                   |
| `show <database>`   |       ✅      |            ✅           |         ✅        | Both              |        ✅        | `pscale api organizations/<org>/databases/<database> --format json`        |
| `create <database>` |       ✅      |            ✅           |         ✅        | Both              |        ✅        | `POST pscale api organizations/<org>/databases --format json`              |
| `delete <database>` |       ✅      |            ✅           |         ✅        | Both              |        ✅        | `DELETE pscale api organizations/<org>/databases/<database> --format json` |
| `dump`              |       ✅      |            ✅           |         ✅        | Vitess            |       N/A       | N/A                                                                        |
| `restore-dump`      |       ✅      |            ✅           |         ✅        | Both              |       N/A       | N/A                                                                        |

```bash theme={null}
export PLANETSCALE_ORG="<org>"
pscale database list --format json
pscale database show <database> --format json
```

Setup and commands to avoid: [CLI overview](../cli.md) · [Service tokens](../api/service-tokens.md#service-token-automation)

> \* *Flag is required*

#### Sub-command flag descriptions

Some of the sub-commands have additional flags unique to the sub-command. This section covers what each of those does. See the above table for which context.

| **Sub-command flag**            | **Description**                                                                                                                       | **Applicable sub-commands** |
| :------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------ | :-------------------------- |
| `--region`                      | Specify the [region](https://planetscale.com/docs/vitess/regions) of the new database. Default is `us-east`.                                                      | `create`                    |
| `--plan`                        | Specify the plan for the database. Currently, `scaler_pro` is the only option and the default.                                        | `create`                    |
| `--cluster_size`                | For Base plan databases, you may specify the cluster size. Default is `PS_10`                                                         | `create`                    |
| `--major-version`               | The major version of the database (Postgres only). Currently supports `17` or `18`.                                                   | `create`                    |
| `--min-storage`                 | Minimum storage size in bytes for Postgres databases using Amazon Elastic Block Storage (EBS).                                        | `create`                    |
| `--max-storage`                 | Maximum storage size in bytes for Postgres databases using Amazon Elastic Block Storage (EBS).                                        | `create`                    |
| `--force`                       | Delete a database without confirmation.                                                                                               | `delete`                    |
| `--local-addr <ADDRESS>`        | Local address to bind and listen for connections. By default the proxy binds to 127.0.0.1 with a random port.                         | `dump`, `restore-dump`      |
| `--threads`                     | Number of concurrent threads to use                                                                                                   | `dump`, `restore-dump`      |
| `--output <DIRECTORY_NAME>`     | Output directory of the dump. By default the dump is saved to a folder in the current directory.                                      | `dump`                      |
| `--tables <TABLES_LIST>`        | Comma separated string of tables to dump. By default, all tables are dumped.                                                          | `dump`                      |
| `--columns <TABLE:COLUMN_LIST>` | Specify which columns to include when dumping specific tables. Use format `table:col1,col2`. Can be specified multiple times.         | `dump`                      |
| `--wheres string`               | Comma separated string of WHERE clauses to filter the tables to dump.                                                                 | `dump`                      |
| `--replica`                     | Dump from a replica (if available; will fail if not).                                                                                 | `dump`                      |
| `--rdonly`                      | Dump from a rdonly tablet (if available; will fail if not).                                                                           | `dump`                      |
| `--keyspace <KEYSPACE_NAME>`    | Optionally target a specific keyspace to be dumped. Useful for sharded databases.                                                     | `dump`                      |
| `--shard <SHARD_NAME>`          | Optional shard to target, must be used with keyspace                                                                                  | `dump`                      |
| `--output-format <FORMAT>`      | Output format for the dump. Options: `sql` (default), `json`, `csv`.                                                                  | `dump`                      |
| `--dir <DIRECTORY_NAME>`        | Directory containing the files to be used for the restore.                                                                            | `restore-dump`              |
| `--overwrite-tables`            | If true, will attempt to DROP TABLE before restoring.                                                                                 | `restore-dump`              |
| `--allow-different-destination` | If true, will allow you to restore the files to a database with a different name without needing to rename the existing dump's files. | `restore-dump`              |
| `--show-details`                | If true, will add extra output during the restore process.                                                                            | `restore-dump`              |
| `--schema-only`                 | If true, will only restore the schema files during the restore process.                                                               | `restore-dump`              |
| `--data-only`                   | If true, will only restore the data files during the restore process.                                                                 | `restore-dump`              |
| `--starting-table <TABLE_NAME>` | Table to start from for the restore (useful for restarting from a certain point)                                                      | `restore-dump`              |
| `--ending-table <TABLE_NAME>`   | Table to end at for the restore (useful for stopping restore at a certain point)                                                      | `restore-dump`              |
| `--web`                         | Perform the action in your web browser                                                                                                | `show`                      |

### Available flags

| **Flag**                    | **Description**                                              |
| :-------------------------- | :----------------------------------------------------------- |
| `-h`, `--help`              | Get help with the `database` command                         |
| `--org <ORGANIZATION_NAME>` | Specify the organization for the database you're acting upon |

### Global flags

| **Command**                     | **Description**                                                                      |
| :------------------------------ | :----------------------------------------------------------------------------------- |
| `--api-token <TOKEN>`           | The API token to use for authenticating against the PlanetScale API.                 |
| `--api-url <URL>`               | The base URL for the PlanetScale API. Default is `https://api.planetscale.com/`.     |
| `--config <CONFIG_FILE>`        | Config file. Default is `$HOME/.config/planetscale/pscale.yml`.                      |
| `--debug`                       | Enable debug mode.                                                                   |
| `-f`, `--format <FORMAT>`       | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. |
| `--no-color`                    | Disable color output.                                                                |
| `--service-token <TOKEN>`       | The service token for authenticating.                                                |
| `--service-token-id <TOKEN_ID>` | The service token ID for authenticating.                                             |

<Note>
  The `--format` flag does not apply to the database dump files created by the `dump` subcommand. However, you can control the output format using the `--output-format` flag, which supports SQL (default), JSON, and CSV formats. When using SQL format, dumps are compatible with [mydumper](https://github.com/mydumper/mydumper).
</Note>

## Examples

### Create a new `scaler_pro` database

**Command:**

```bash theme={null}
pscale database create new-database --region <REGION_NAME> --plan scaler_pro --cluster_size PS_80
```

**Output:**

Database `new-database` was successfully created.

### Create a Postgres database with storage size settings (Amazon EBS)

Use `--min-storage` and `--max-storage` to set the initial and maximum storage size in bytes when creating a Postgres database on Amazon Elastic Block Storage (EBS).

**Command:**

```bash theme={null}
pscale database create new-postgres-db --engine postgres --region <REGION_NAME> --min-storage 10737418240 --max-storage 21474836480
```

In this example, the database starts at 10 GiB (`10737418240` bytes) and can scale up to 20 GiB (`21474836480` bytes).

### Create a dump of an existing branch:

This command is only available for Vitess databases. For Postgres databases, use the [pg\_dump](https://www.postgresql.org/docs/current/app-pgdump.html) command instead.

**Command:**

```bash theme={null}
pscale database dump <CURRENT_DATABASE_NAME> <BRANCH_NAME> --output="<DIRECTORY_FOR_BACKUP>" --org="<ORIGINAL_ORGANIZATION>"
```

**Output:**

A local export of your database will be generated within the current directory by default but since we are providing an `--output` location above that will be used instead.

### Export data in different formats:

You can specify the output format when dumping your Vitess database using the `--output-format` flag:

**Export as JSON:**

```bash theme={null}
pscale database dump <DATABASE_NAME> <BRANCH_NAME> --output-format=json
```

**Export as CSV:**

```bash theme={null}
pscale database dump <DATABASE_NAME> <BRANCH_NAME> --output-format=csv
```

**Export as SQL (default):**

```bash theme={null}
pscale database dump <DATABASE_NAME> <BRANCH_NAME> --output-format=sql
```

### Dump specific columns from tables:

You can use the `--columns` flag to export only specific columns from your tables. This is useful when you need a partial export or want to exclude sensitive data:

```bash theme={null}
# Export only specific columns from multiple tables
pscale database dump <DATABASE_NAME> <BRANCH_NAME> --columns 'users:id,name' --columns 'orders:id,total'
```

The schema dump will include the complete table schema, but the data files will only contain the specified columns.

### Restore a backup to an existing branch:

**Command:**

```bash theme={null}
pscale database restore-dump <DESTINATION_DATABASE_NAME> <BRANCH_NAME> --dir="<DIRECTORY_FOR_BACKUP>" --org="<DESTINATION_ORGANIZATION>"
```

**Output:**

You should receive output indicating the restore is progressing until it completes successfully.

The command above will allow you to restore an existing backup to another branch located either within the same organization/database as the original, or within a completely different organization/database.

As of `pscale` v0.218.0 or newer the `--allow-different-destination` flag is now available. If this flag is provided it will make the steps below about renaming the files unnecessary.

If you opt to import into a database with a different name you will have to make sure you rename the files from your backup beforehand.

For example, the files will be named something like this:

```bash theme={null}
<CURRENT_DATABASE_NAME>.<TABLE_NAME>-schema.sql
```

And you will want to rename all of the files in the dump folder to have the new database name if it is not the same as the existing one:

```bash theme={null}
<DESTINATION_DATABASE_NAME>.<TABLE_NAME>-schema.sql
```

If importing into a branch that already contains table definitions that you want to overwrite, you may also be required to pass in the optional `--overwrite-tables` flag.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
