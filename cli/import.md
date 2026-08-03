---
url: https://planetscale.com/docs/cli/import
title: "Import"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale CLI commands: import

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

<PlatformAvailability current="postgres" />

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you've installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The `import` command

This command imports external databases into PlanetScale Postgres. Today it supports **Cloudflare D1** (SQLite) through the `d1` sub-command group. For other migration paths, see the [Postgres imports overview](../postgres/imports/postgres-imports.md).

**Usage:**

```bash theme={null}
pscale import d1 <SUB-COMMAND> <FLAG>
```

Branch-scoped sub-commands take the positional form `<DATABASE_NAME> [BRANCH_NAME]`, defaulting to the default branch. The organization comes from your pscale config (`pscale org`) or the `--org` flag.

<Note>
  `pscale import d1` supports the `human` (default) and `json` output formats. The `csv` format is not supported.
</Note>

### Available sub-commands

| **Sub-command**                             | **Sub-command flags**                                                                                             | **Product** | **Description**                                                        |
| :------------------------------------------ | :---------------------------------------------------------------------------------------------------------------- | :---------- | :--------------------------------------------------------------------- |
| `d1 doctor`                                 |                                                                                                                   | Postgres    | Check that pgloader, psql, and other prerequisites are installed       |
| `d1 lint`                                   | `--input <FILE>`                                                                                                  | Postgres    | Analyze a D1 SQL export for migration issues                           |
| `d1 convert-schema`                         | `--input <FILE>`, `--output <FILE>`                                                                               | Postgres    | Convert SQLite schema in a D1 export to PostgreSQL DDL                 |
| `d1 start <DATABASE_NAME> [BRANCH_NAME]`    | `--input <FILE>`, `--dry-run`, `--migration-id <MIGRATION_ID>`, `--method <METHOD>`, `--dbname <NAME>`, `--force` | Postgres    | Start importing a D1 export (lint + plan, then load)                   |
| `d1 verify <DATABASE_NAME> [BRANCH_NAME]`   | `--migration-id <MIGRATION_ID>`, `--input <FILE>`, `--sqlite <FILE>`, `--dbname <NAME>`                           | Postgres    | Verify the import: row counts, sequences, coercion, and content checks |
| `d1 status <DATABASE_NAME> [BRANCH_NAME]`   | `--migration-id <MIGRATION_ID>`                                                                                   | Postgres    | Show local migration state                                             |
| `d1 complete <DATABASE_NAME> [BRANCH_NAME]` | `--migration-id <MIGRATION_ID>`, `--force`                                                                        | Postgres    | Mark a D1 migration as complete in local state                         |

#### Sub-command flag descriptions

Some of the sub-commands have additional flags unique to the sub-command. This section covers what each of those does. See the above table for which context.

| **Sub-command flag**            | **Type** | **Description**                                                                                                                                              | **Applicable sub-commands**                 |
| :------------------------------ | :------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------ |
| `--input <FILE>`                | string   | Path to the D1 SQL export produced by `wrangler d1 export` (required for `lint`, `convert-schema`, and `start`)                                              | `lint`, `convert-schema`, `start`, `verify` |
| `--output <FILE>`               | string   | Output PostgreSQL schema file (default `<INPUT>.postgres.sql`)                                                                                               | `convert-schema`                            |
| `--dry-run`                     | boolean  | Lint and build the import plan without loading Postgres; prints a migration ID for the real run                                                              | `start`                                     |
| `--migration-id <MIGRATION_ID>` | string   | Migration ID from a prior `start --dry-run` (required for `verify`, `status`, and `complete`)                                                                | `start`, `verify`, `status`, `complete`     |
| `--method <METHOD>`             | string   | Import method: `pgloader` (exports ≥ 1 GB) or `psql` (exports \< 1 GB; schema via psql, data via pgloader). Chosen automatically by export size when omitted | `start`                                     |
| `--dbname <NAME>`               | string   | Destination PostgreSQL database name (default `postgres`)                                                                                                    | `start`, `verify`                           |
| `--sqlite <FILE>`               | string   | Path to a local SQLite file to use for source row counts                                                                                                     | `verify`                                    |
| `--force`                       | boolean  | Skip the confirmation prompt                                                                                                                                 | `start`, `complete`                         |

### Available flags

| **Flag**                    | **Description**                       |
| :-------------------------- | :------------------------------------ |
| `-h`, `--help`              | View help for import command          |
| `--org <ORGANIZATION_NAME>` | The organization for the current user |

### Global flags

| **Command**                     | **Description**                                                                  |
| :------------------------------ | :------------------------------------------------------------------------------- |
| `--api-token <TOKEN>`           | The API token to use for authenticating against the PlanetScale API.             |
| `--api-url <URL>`               | The base URL for the PlanetScale API. Default is `https://api.planetscale.com/`. |
| `--config <CONFIG_FILE>`        | Config file. Default is `$HOME/.config/planetscale/pscale.yml`.                  |
| `--debug`                       | Enable debug mode.                                                               |
| `-f`, `--format <FORMAT>`       | Show output in a specific format. Possible values: `human` (default), `json`.    |
| `--no-color`                    | Disable color output.                                                            |
| `--service-token <TOKEN>`       | The service token for authenticating.                                            |
| `--service-token-id <TOKEN_ID>` | The service token ID for authenticating.                                         |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
