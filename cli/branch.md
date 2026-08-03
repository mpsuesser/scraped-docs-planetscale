---
url: https://planetscale.com/docs/cli/branch
title: "Branch"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale CLI commands: branch

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

## The `branch` command

This command allows you to create, delete, diff, and manage [branches](../vitess/schema-changes/branching.md).

**Usage:**

```bash theme={null}
pscale branch <SUB-COMMAND> <FLAG>
```

### Available sub-commands

| **Sub-command**                                         | **Sub-command flags**                                                                                                      | **Description**                                                                                                         | **Product**      |
| :------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------- | :--------------- |
| `connections <COMMAND>`                                 | `show`, `top`, `kill`, `kill-transaction`                                                                                  | Inspect and act on live branch connections. See the [`connections` reference](connections.md).                        | Postgres, Vitess |
| `create <DATABASE_NAME> <BRANCH_NAME>`                  | `--from <SOURCE_BRANCH>`, `--region <BRANCH_REGION>`, `--restore <BACKUP_NAME>`, `--seed-data`, `--wait`                   | Create a new branch on the specified database                                                                           | Postgres, Vitess |
| `delete <DATABASE_NAME> <BRANCH_NAME>`                  | `--force`, `--delete-descendants`                                                                                          | Delete the specified branch from a database                                                                             | Postgres, Vitess |
| `diff <DATABASE_NAME> <BRANCH_NAME>`                    | `--web`                                                                                                                    | Show the diff of the specified branch against the parent branch.                                                        | Vitess           |
| `list <DATABASE_NAME>`                                  | `--web`                                                                                                                    | List all branches of a database                                                                                         | Postgres, Vitess |
| `parameters list <DATABASE_NAME> <BRANCH_NAME>`         | `--namespace <NAMESPACE>`, `--extension`, `--internal`                                                                     | List the configuration parameters of a Postgres branch                                                                  | Postgres         |
| `promote <DATABASE_NAME> <BRANCH_NAME>`                 |                                                                                                                            | Promote a database branch to production                                                                                 | Vitess           |
| `query-patterns download <DATABASE_NAME> <BRANCH_NAME>` | `--output <PATH>`                                                                                                          | Generate and download a CSV report of branch query patterns. See the [`query-patterns` reference](query-patterns.md). | Postgres, Vitess |
| `refresh-schema <DATABASE_NAME> <BRANCH_NAME>`          |                                                                                                                            | Refresh the schema for a database branch                                                                                | Vitess           |
| `resize <DATABASE_NAME> <BRANCH_NAME>`                  | `--cluster-size <SKU>`, `--replicas <COUNT>`, `--parameters <NAMESPACE.NAME=VALUE>`, `--wait`, `--wait-timeout <DURATION>` | Change a Postgres branch's cluster size, replica count, or configuration parameters                                     | Postgres         |
| `resize status <DATABASE_NAME> <BRANCH_NAME>`           |                                                                                                                            | Show the latest change request for a Postgres branch                                                                    | Postgres         |
| `resize cancel <DATABASE_NAME> <BRANCH_NAME>`           |                                                                                                                            | Cancel the queued change request for a Postgres branch                                                                  | Postgres         |
| `safe-migrations enable <DATABASE_NAME> <BRANCH_NAME>`  |                                                                                                                            | Enables safe migrations for a database branch                                                                           | Vitess           |
| `safe-migrations disable <DATABASE_NAME> <BRANCH_NAME>` |                                                                                                                            | Disables safe migrations for a database branch                                                                          | Vitess           |
| `schema <DATABASE_NAME> <BRANCH_NAME>`                  | `--web`                                                                                                                    | Show the schema of a branch                                                                                             | Vitess           |
| `show <DATABASE_NAME> <BRANCH_NAME>`                    | `--web`                                                                                                                    | Show a specific backup of a branch                                                                                      | Postgres, Vitess |
| `switch <BRANCH_NAME> --database <DATABASE_NAME>`       | `--database <DATABASE_NAME>`\*, `--create`, `parent-branch <BRANCH_NAME>`                                                  | Switch to the specified branch                                                                                          | Postgres, Vitess |

### Service token automation: `branch`

Legend: ✅ supported · 🚫 unavailable · 👤 interactive login only. All sub-commands require `--org` or `PLANETSCALE_ORG`.

| Sub-command                  | Env-var auth | `--service-token` flag | Requires `--org` | Postgres / Vitess | `--format json` | API equivalent                                                                                                                                                                                         |
| :--------------------------- | :----------: | :--------------------: | :--------------: | :---------------- | :-------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `list <database>`            |       ✅      |            ✅           |         ✅        | Both              |        ✅        | `pscale api organizations/<org>/databases/<database>/branches --format json`                                                                                                                           |
| `show <database> <branch>`   |       ✅      |            ✅           |         ✅        | Both              |        ✅        | `pscale api organizations/<org>/databases/<database>/branches/<branch> --format json`                                                                                                                  |
| `create` / `delete`          |       ✅      |            ✅           |         ✅        | Both              |        ✅        | `POST pscale api organizations/<org>/databases/<database>/branches --format json` · `DELETE pscale api organizations/<org>/databases/<database>/branches/<branch> --format json`                       |
| `schema <database> <branch>` |       ✅      |            ✅           |         ✅        | Both              |        ✅        | `pscale api organizations/<org>/databases/<database>/branches/<branch>/schema --format json`                                                                                                           |
| `diff` / `promote`           |       ✅      |            ✅           |         ✅        | Vitess            |        ✅        | `pscale api organizations/<org>/databases/<database>/branches/<branch>/schema/lint --format json` · `POST pscale api organizations/<org>/databases/<database>/branches/<branch>/promote --format json` |
| `connections`                |       ✅      |            ✅           |         ✅        | Both              |        ✅        | `pscale branch connections top <database> <branch> --org <org> --format json`                                                                                                                          |

```bash theme={null}
export PLANETSCALE_ORG="<org>"
pscale branch list <database> --format json
pscale branch show <database> <branch> --format json
```

Setup and commands to avoid: [CLI overview](../cli.md) · [Service tokens](../api/service-tokens.md#service-token-automation)

> \* *Flag is required*

#### Sub-command flag descriptions

Some of the sub-commands have additional flags unique to the sub-command. This section covers what each of those does. See the above table for which context.

| **Sub-command flag**                  | **Description**                                                                                                                                                                 | **Applicable sub-commands**                |
| :------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :----------------------------------------- |
| `--from <SOURCE_BRANCH>`              | Parent branch that you want to create a new branch off of                                                                                                                       | `create`                                   |
| `--region <BRANCH_REGION>`            | Region where database should be created                                                                                                                                         | `create`                                   |
| `--restore <BACKUP_NAME>`             | Create a new branch from a specified backup                                                                                                                                     | `create`                                   |
| `--seed-data`                         | Create a new branch and seed data using the [Data Branching® feature](../vitess/schema-changes/data-branching.md)                                                                    | `create`                                   |
| `--web`                               | Perform the action in your web browser                                                                                                                                          | `create`, `diff`, `list`, `schema`, `show` |
| `--wait`                              | Wait until the branch is ready (`create`) or the change request completes (`resize`)                                                                                            | `create`, `resize`                         |
| `--major-version`                     | The major version of the branch (Postgres only). Currently supports `17` or `18`.                                                                                               | `create`                                   |
| `--database <DATABASE_NAME>`          | Specify the database name                                                                                                                                                       | `switch`                                   |
| `--create`                            | Create a new branch if it does not exist                                                                                                                                        | `switch`                                   |
| `--parent-branch <BRANCH_NAME>`       | If a new branch is being created, use this to specify a parent branch. Default is `main`.                                                                                       | `switch`                                   |
| `--delete-descendants`                | Recursively delete all descendant branches when deleting a branch                                                                                                               | `delete`                                   |
| `--output <PATH>`                     | Write the query patterns CSV report to a specific file path.                                                                                                                    | `query-patterns download`                  |
| `--cluster-size <SKU>`                | New cluster size for the branch, as a fully-qualified SKU name (e.g. `PS_10_GCP_X86`). Use `pscale size cluster list --engine postgresql` to see the valid sizes.               | `resize`                                   |
| `--replicas <COUNT>`                  | Desired number of replicas for the branch                                                                                                                                       | `resize`                                   |
| `--parameters <NAMESPACE.NAME=VALUE>` | Set a configuration parameter (e.g. `pgconf.max_connections=200`). Repeat the flag to set multiple parameters. Use `pscale branch parameters list` to see available parameters. | `resize`                                   |
| `--wait-timeout <DURATION>`           | Maximum time to wait for the change request to complete with `--wait`. Default is `10m`.                                                                                        | `resize`                                   |
| `--namespace <NAMESPACE>`             | Only show parameters in this namespace (e.g. `pgconf`, `pgbouncer`, `patroni`)                                                                                                  | `parameters list`                          |
| `--extension`                         | Only show parameters that configure an extension (`--extension=false` hides them)                                                                                               | `parameters list`                          |
| `--internal`                          | Only show internal (immutable) parameters (`--internal=false` hides them)                                                                                                       | `parameters list`                          |

<Note>
  The `--region` flag can not be used with `--restore` when creating a branch. Branch backups will be restored to their original region.
</Note>

### Available flags

| **Flag**                    | **Description**                       |
| :-------------------------- | :------------------------------------ |
| `-h`, `--help`              | View help for auth command            |
| `--org <ORGANIZATION_NAME>` | The organization for the current user |

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

## Examples

### The `connections` sub-command

**Command:**

```bash theme={null}
pscale branch connections top <database> <branch>
```

Opens a live view of the branch's connection activity. It works for Postgres and Vitess branches; for
Vitess, pass `--keyspace` and `--shard` to target a tablet, or run interactively to select them. See
the [`connections` reference](connections.md), [Inspect live Postgres connections](../postgres/monitoring/connections.md),
and [Inspect live Vitess connections](../vitess/monitoring/connections.md) for the full workflow.

### The `query-patterns` sub-command

**Command:**

```bash theme={null}
pscale branch query-patterns download <DATABASE_NAME> <BRANCH_NAME> --org <ORGANIZATION_NAME>
```

Creates a Query Insights report for the branch, waits for it to finish generating, and downloads the
CSV file. Use `--output` to choose the file path. See the [`query-patterns` reference](query-patterns.md)
for the full workflow.

### The `list` sub-command with `--web` flag

**Command:**

```bash theme={null}
pscale branch list <DATABASE_NAME> --web
```

**Output:**

Opens the Branches page, `<https://app.planetscale.com/org/database/branches>`, in browser.

### The `parameters` sub-command

**Command:**

```bash theme={null}
pscale branch parameters list <DATABASE_NAME> <BRANCH_NAME> --namespace pgconf
```

Lists the configuration parameters of a Postgres branch, including their current values, defaults,
allowed ranges, and whether changing them requires a restart. Use `--namespace` to limit the output
to a single namespace such as `pgconf`, `pgbouncer`, or `patroni`.

### The `resize` sub-command

**Command:**

```bash theme={null}
pscale branch resize <DATABASE_NAME> <BRANCH_NAME> --parameters pgconf.max_connections=200 --parameters pgconf.work_mem=8MB --wait
```

Creates a change request for a Postgres branch. A single change request can update the cluster size
(`--cluster-size`), the number of replicas (`--replicas`), and configuration parameters
(`--parameters`, repeatable) together. With `--wait`, the command polls until the change request
completes; without it, the command returns immediately and you can follow up with `resize status`.

Some parameters require a database restart to take effect; the command tells you which ones before
the change is applied.

**Command:**

```bash theme={null}
pscale branch resize status <DATABASE_NAME> <BRANCH_NAME>
```

Shows the latest change request for the branch, including its state (`queued`, `pending`,
`resizing`, `completed`, or `canceled`) and what it changes.

**Command:**

```bash theme={null}
pscale branch resize cancel <DATABASE_NAME> <BRANCH_NAME>
```

Cancels the queued change request for the branch. Only change requests that have not started being
applied can be canceled.

### The `diff` sub-command

**Command:**

```bash theme={null}
pscale branch diff <DATABASE_NAME> <BRANCH_NAME>
```

**Output:**

```sql theme={null}
-- users --
+CREATE TABLE `users` (
+  `id` bigint unsigned NOT NULL AUTO_INCREMENT,
+  `name` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
+  PRIMARY KEY (`id`)
+) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

This will return the diff against the parent branch.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
