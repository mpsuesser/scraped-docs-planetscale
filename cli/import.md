---
url: https://planetscale.com/docs/cli/import
title: "Import"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The import command

This command imports external databases into PlanetScale Postgres. Today it supports **Cloudflare D1** (SQLite) through the `d1` sub-command group. For other migration paths, see the [Postgres imports overview](../postgres/imports/postgres-imports.md).

**Usage:**

```shellscript
pscale import d1 <SUB-COMMAND> <FLAG>
```

Branch-scoped sub-commands take the positional form `<DATABASE_NAME> [BRANCH_NAME]`, defaulting to the default branch. The organization comes from your pscale config (`pscale org`) or the `--org` flag.

`pscale import d1` supports the `human` (default) and `json` output formats. The `csv` format is not supported.

### Available sub-commands

| **Sub-command** | **Sub-command flags** | **Product** | **Description** |
| --- | --- | --- | --- |
| `d1 doctor` |  | Postgres | Check that pgloader, psql, and other prerequisites are installed |
| `d1 lint` | `--input <FILE>` | Postgres | Analyze a D1 SQL export for migration issues |
| `d1 convert-schema` | `--input <FILE>`, `--output <FILE>` | Postgres | Convert SQLite schema in a D1 export to PostgreSQL DDL |
| `d1 start <DATABASE_NAME> [BRANCH_NAME]` | `--input <FILE>`, `--dry-run`, `--migration-id <MIGRATION_ID>`, `--method <METHOD>`, `--dbname <NAME>`, `--force` | Postgres | Start importing a D1 export (lint + plan, then load) |
| `d1 verify <DATABASE_NAME> [BRANCH_NAME]` | `--migration-id <MIGRATION_ID>`, `--input <FILE>`, `--sqlite <FILE>`, `--dbname <NAME>` | Postgres | Verify the import: row counts, sequences, coercion, and content checks |
| `d1 status <DATABASE_NAME> [BRANCH_NAME]` | `--migration-id <MIGRATION_ID>` | Postgres | Show local migration state |
| `d1 complete <DATABASE_NAME> [BRANCH_NAME]` | `--migration-id <MIGRATION_ID>`, `--force` | Postgres | Mark a D1 migration as complete in local state |

#### Sub-command flag descriptions

Some of the sub-commands have additional flags unique to the sub-command. This section covers what each of those does. See the above table for which context.

| **Sub-command flag** | **Type** | **Description** | **Applicable sub-commands** |
| --- | --- | --- | --- |
| `--input <FILE>` | string | Path to the D1 SQL export produced by `wrangler d1 export` (required for `lint`, `convert-schema`, and `start`) | `lint`, `convert-schema`, `start`, `verify` |
| `--output <FILE>` | string | Output PostgreSQL schema file (default `<INPUT>.postgres.sql`) | `convert-schema` |
| `--dry-run` | boolean | Lint and build the import plan without loading Postgres; prints a migration ID for the real run | `start` |
| `--migration-id <MIGRATION_ID>` | string | Migration ID from a prior `start --dry-run` (required for `verify`, `status`, and `complete`) | `start`, `verify`, `status`, `complete` |
| `--method <METHOD>` | string | Import method: `pgloader` (exports ≥ 1 GB) or `psql` (exports < 1 GB; schema via psql, data via pgloader). Chosen automatically by export size when omitted | `start` |
| `--dbname <NAME>` | string | Destination PostgreSQL database name (default `postgres`) | `start`, `verify` |
| `--sqlite <FILE>` | string | Path to a local SQLite file to use for source row counts | `verify` |
| `--force` | boolean | Skip the confirmation prompt | `start`, `complete` |

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for import command |
| `--org <ORGANIZATION_NAME>` | The organization for the current user |

### Global flags

| **Command** | **Description** |
| --- | --- |
| `--api-token <TOKEN>` | The API token to use for authenticating against the PlanetScale API. |
| `--api-url <URL>` | The base URL for the PlanetScale API. Default is `https://api.planetscale.com/`. |
| `--config <CONFIG_FILE>` | Config file. Default is `$HOME/.config/planetscale/pscale.yml`. |
| `--debug` | Enable debug mode. |
| `-f`, `--format <FORMAT>` | Show output in a specific format. Possible values: `human` (default), `json`. |
| `--no-color` | Disable color output. |
| `--service-token <TOKEN>` | The service token for authenticating. |
| `--service-token-id <TOKEN_ID>` | The service token ID for authenticating. |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
