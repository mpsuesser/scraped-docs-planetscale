---
url: https://planetscale.com/docs/neki/imports/postgres
title: "Postgres"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Import Postgres into a new, unsharded Neki database:

1. Stop source writes and create a custom-format dump with `pg_dump`.
2. Restore into a fresh Neki database with `pg_restore`.
3. Validate the imported database and move application traffic to Neki.

The import is offline. The source application must stop writing before the dump and remain stopped until import validation and cutover are complete.

Automated Neki imports are not available during Platform Preview. This guide describes the manual dump-and-restore path. To have PlanetScale help plan and run the migration, email [migrations@planetscale.com](mailto:migrations@planetscale.com).

Restore into an unsharded Neki database. Although Neki supports some sharded and reference-table `COPY FROM STDIN` operations, `pg_restore` is not a topology-aware sharded import workflow. See [COPY limits](../platform-preview-limitations.md#copy-limits).

## Assess the source

Before scheduling the migration, record:

- The Postgres version and total database size.
- The largest tables and indexes and the expected growth during the migration.
- Primary keys, foreign keys, checks, unique constraints, and indexes.
- Identity and serial columns, owned sequences, enums, JSONB values, timestamps, views, and quoted identifiers.
- Schemas, roles, ownership, grants, and default privileges.
- Installed extensions and their versions.
- The time for which application and background writes can remain stopped.

Inventory extensions on the source before creating the dump:

```sql
SELECT extname, extversion
FROM pg_extension
WHERE extname <> 'plpgsql'
ORDER BY extname;
```

Compare every result with the target configuration profile’s **Extensions** tab. The dashboard catalog is the authoritative list of extensions supported for that profile. Confirm any required profile-level enablement and `CREATE EXTENSION` workflow before the migration. An unsupported extension statement can cause `pg_restore --exit-on-error` to stop before loading data.

Time a representative dump, restore, and validation run with a similar data volume so that the maintenance window includes more than data transfer.

## Prepare the target

1. Create the Neki database and wait for its default branch to become ready.
2. Keep the initial one-shard, unsharded data topology for the import.
3. Create the application roles needed on the target and test their connections.
4. Complete any required profile-level extension enablement and decide whether `pg_restore` or a separate administrative step will install each extension’s database objects. See [Postgres extensions](../extensions.md).
5. Confirm that the target is empty and that no application is writing to it.

## Stop writes and create the dump

Put the source application into maintenance or read-only mode. Verify that background workers, scheduled jobs, and other clients cannot write, then keep source writes stopped through the remaining steps.

The recommended path uses a custom-format dump without source ownership or access-control commands:

```shellscript
pg_dump \
  --format=custom \
  --no-owner \
  --no-privileges \
  --verbose \
  --file=source.dump \
  "$SOURCE_DATABASE_URL"
```

Keep the source database and dump available until the rollback window closes.

## Restore into Neki

Restore with the target branch’s generated connection string:

```shellscript
pg_restore \
  --dbname="$NEKI_DATABASE_URL" \
  --no-owner \
  --no-privileges \
  --exit-on-error \
  --verbose \
  --jobs=4 \
  source.dump
```

Use a custom-format dump with `pg_restore` for the recommended import path. The example runs four restore jobs in parallel. Omit `--jobs=4` to run the restore serially. If you created a plain-text SQL dump instead, restore it with `psql`. Review the complete verbose output before continuing.

After the restore, collect statistics for query planning:

```sql
ANALYZE;
```

## Validate the target

Keep both databases unavailable for writes while you compare:

- Expected schemas, tables, views, indexes, and constraints.
- Exact row counts for critical tables and aggregate counts for every table.
- Checksums or deterministic fingerprints for critical business data.
- Representative application reads, writes, transactions, and query plans.
- Inserts that exercise identity and sequence-backed columns.
- Role permissions and TLS connections from the production runtime.

If any row count or checksum differs, do not cut over.

## Cut over

1. Confirm that source writes are still stopped and validation is complete.
2. Update application secrets to use the Neki branch connection string.
3. Start a small number of application instances and run smoke tests.
4. Increase traffic while watching router errors, query latency, connections, CPU, storage, and Postgres errors.
5. Keep the old source read-only until the rollback boundary has passed.

Avoid schema or topology changes during cutover. Establish a stable Neki baseline before planning later changes.

## Roll back

For this offline path, rollback means stopping writes to Neki and reconnecting the application to the unchanged source. Writes accepted by Neki after cutover are not copied back automatically. Decide before cutover whether those writes can be discarded or require a separate reconciliation procedure.

See [Migration troubleshooting](troubleshooting.md) for common dump, restore, and validation failures.

An imported database can stay unsharded. To shard tables that already have rows, see [Data migration](../data-migration.md#reshard-existing-tables).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
