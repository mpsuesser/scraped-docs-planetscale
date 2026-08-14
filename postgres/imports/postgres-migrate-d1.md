---
url: https://planetscale.com/docs/postgres/imports/postgres-migrate-d1
title: "Postgres Migrate D1"
description: ""
access_date: 2026-08-14T17:16:03.479Z
current_date: 2026-08-14T17:16:03.479Z
---

Use this guide to move an existing **Cloudflare D1** database to **PlanetScale Postgres**. The PlanetScale CLI converts SQLite schema to Postgres, loads your data, and verifies the result.

This is an **offline** migration: export D1, import into PlanetScale, then point your app at the new database.

Want help planning the move? PlanetScale’s [migration services](https://planetscale.com/migrate) can walk through export, import, and cutover with you.

## Overview

The workflow has three parts:

1. Export your remote D1 database with **Wrangler**
2. Lint and import the SQL dump with **`pscale import d1`**
3. Verify row counts, then update your application connection string

## Prerequisites

Before you start, install and configure:

- **[PlanetScale CLI](../../cli.md)** — authenticated to your org (`pscale auth login`, then `pscale org set <org>`)
- **[Cloudflare Wrangler](https://developers.cloudflare.com/workers/wrangler/)** — to export the remote D1 database
- **[pgloader](https://pgloader.io/)** — required for loading data (used for all import sizes)
- **`psql`** — PostgreSQL client (version 10 or newer)
- **`sqlite3`** *(optional)* — helps with deeper verification checks

You also need:

- A **PlanetScale Postgres** database with enough storage for the imported data
- A **target branch** that is empty or disposable (the import replaces schema on that branch)
- **Cloudflare credentials** for remote export (`CLOUDFLARE_API_TOKEN` and `CLOUDFLARE_ACCOUNT_ID`), unless you already have a D1 SQL export file

Check that your machine is ready:

```shellscript
pscale import d1 doctor
```

Fix anything marked as failed before continuing.

## Step 1: Export D1 with Wrangler

Export your production D1 database to a SQL file:

```shellscript
wrangler d1 export <database-name> --remote --output ./d1-export.sql
```

Keep this file — every later `pscale import d1` command uses `--input ./d1-export.sql`.

## Step 2: Lint the export

Review schema and data compatibility issues before touching Postgres:

```shellscript
pscale import d1 lint --input ./d1-export.sql
```

Fix **errors** before importing. **Warnings** (for example integer booleans or text timestamps) are usually handled automatically during import.

## Step 3: Preview the import

Run a dry run on the default branch to lint again, build the import plan, and get a migration ID:

```shellscript
pscale import d1 start my-database --input ./d1-export.sql --dbname myapp --dry-run
```

`my-database` is the PlanetScale database (the cluster). `--dbname` is the Postgres database *inside* it. If you omit `--dbname`, data lands in `postgres`. Use the same `--dbname` on `start` and `verify`.

Use `--format json` if you want machine-readable output. Save the `migration_id` from the response.

The command above targets the default branch (`main`). To target a different branch, include the branch name after the database name:

```shellscript
pscale import d1 start my-database <branch> --input ./d1-export.sql --dbname myapp --dry-run
```

## Step 4: Import into PlanetScale Postgres

Run the import against your PlanetScale database:

```shellscript
pscale import d1 start my-database --input ./d1-export.sql --dbname myapp --migration-id <migration-id>
```

The CLI converts SQLite DDL to Postgres, loads data with pgloader, and creates indexes after the load.

Use `--force` to skip the confirmation prompt in scripts.

## Step 5: Verify the import

Compare source and destination row counts and run spot checks:

```shellscript
pscale import d1 verify my-database --migration-id <migration-id> --input ./d1-export.sql --dbname myapp
```

Check migration progress at any time:

```shellscript
pscale import d1 status my-database --migration-id <migration-id>
```

## Step 6: Complete and cut over

When verification passes, mark the migration complete:

```shellscript
pscale import d1 complete my-database --migration-id <migration-id>
```

Update your application to use the PlanetScale Postgres connection string from the console.

## Important notes

### ORM migration tables are skipped

`pscale import d1` **does not import** ORM or framework migration bookkeeping tables. These SQLite-only history tables are not valid on Postgres and are excluded from schema conversion and data load.

Examples the linter detects and the importer skips:

- Drizzle — `__drizzle_migrations` and related tables
- Prisma — `_prisma_migrations`
- Knex — `knex_migrations`, `knex_migrations_lock`
- Rails — `schema_migrations`, `ar_internal_metadata`
- Django — `django_migrations`
- Sequelize, Flyway, Liquibase, Alembic, TypeORM, Goose — their version/history tables

**After import, re-baseline your ORM on Postgres** (for example `prisma db pull` + a fresh migration, or `drizzle-kit push`). Do not expect SQLite migration history to carry over.

Run `pscale import d1 lint --input ./d1-export.sql` to see which tables in your export match these patterns.

### Plan for downtime

This path is a **point-in-time snapshot**. Applications should stop writing to D1 before export, or you accept that rows written after export will not be on PlanetScale.

### Storage and branch choice

Size your PlanetScale database for the full imported dataset. Run the import on a branch you can replace if something goes wrong.

### Application table name collisions

If you have an application table named `schema_migrations` that is **not** Rails migration metadata, lint may warn about a name collision. Rename that table in D1 before export, or confirm lint output carefully so real data is not skipped by mistake.

## Next steps

## Postgres import overview

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
