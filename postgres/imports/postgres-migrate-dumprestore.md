---
url: https://planetscale.com/docs/postgres/imports/postgres-migrate-dumprestore
title: "Postgres Migrate Dumprestore"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

Before beginning your migration, we recommend running our [migration assessment tool](https://planetscale.com/liftoff) for instant feedback on migration complexity, potential blockers, and the recommended migration path.

The [pg\_dump](https://www.postgresql.org/docs/current/app-pgdump.html) and restore method is the simplest approach for migrating PostgreSQL databases to PlanetScale Postgres. This method is ideal for smaller databases that can tolerate downtime during the migration process.

Want expert guidance for your migration? PlanetScale’s [migration services](https://planetscale.com/migrate) are available to help you plan and execute a smooth, successful move.

## Overview

This migration method involves:

1. Creating a full backup of your source database using `pg_dump`
2. Transferring the dump file to your local environment or staging area
3. Restoring the database to PlanetScale Postgres using `pg_restore` or `psql`

## Prerequisites

Before starting the migration:

- Ensure you have PostgreSQL client tools installed (`pg_dump`, `pg_restore`, `psql`)
- Have read access to your source PostgreSQL database
- Have connection details for your PlanetScale Postgres database from the console
- Ensure the disk on your PlanetScale database has at least 150% of the capacity of your source database. If you are migrating to a PlanetScale database backed by network-attached storage, you can [resize](../cluster-configuration/cluster-storage.md) your disk manually by setting the “Minimum disk size.” If you are using Metal, you will need to select a size when first creating your database. For example, if your source database is 330GB, you should have at least 500GB of storage available on PlanetScale.
- Verify sufficient storage space for the dump file
- Plan for application downtime during the migration

## Step 1: Create Database Dump

### For a complete database dump:

```shellscript
pg_dump -h source-host \
        -p source-port \
        -U source-username \
        -d source-database \
        --verbose \
        --no-owner \
        --no-privileges \
        -f database_dump.sql
```

### For a custom format dump (recommended for large databases):

```shellscript
pg_dump -h source-host \
        -p source-port \
        -U source-username \
        -d source-database \
        --verbose \
        --no-owner \
        --no-privileges \
        -Fc \
        -f database_dump.dump
```

### Command options explained:

- `--verbose`: Provides detailed output during the dump process
- `--no-owner`: Excludes ownership information from the dump
- `--no-privileges`: Excludes privilege information from the dump
- `-Fc`: Creates a custom format dump (binary, compressed)
- `-f`: Specifies the output file name

## Step 2: Get PlanetScale Connection Details

From your PlanetScale console:

## Step 3: Restore to PlanetScale Postgres

### For SQL format dumps:

```shellscript
psql -h planetscale-host \
     -p planetscale-port \
     -U planetscale-username \
     -d planetscale-database \
     -f database_dump.sql
```

### For custom format dumps:

```shellscript
pg_restore -h planetscale-host \
           -p planetscale-port \
           -U planetscale-username \
           -d planetscale-database \
           --verbose \
           --no-owner \
           --no-privileges \
           database_dump.dump
```

### For parallel restoration (faster for large databases):

```shellscript
pg_restore -h planetscale-host \
           -p planetscale-port \
           -U planetscale-username \
           -d planetscale-database \
           --verbose \
           --no-owner \
           --no-privileges \
           --jobs=4 \
           database_dump.dump
```

## Step 4: Verify Migration

After the restore completes, verify your migration:

### Check table counts:

```sql
SELECT schemaname, relname, n_tup_ins as row_count
FROM pg_stat_user_tables
ORDER BY schemaname, relname;
```

### Verify data integrity:

```sql
-- Check a few sample records from key tables
SELECT count(*) FROM your_main_table;
SELECT * FROM your_main_table LIMIT 5;
```

### Check for errors in logs:

Review the output from the pg\_restore command for any errors or warnings.

## Troubleshooting

### Common Issues:

**Permission errors:**

- Ensure your PlanetScale user has appropriate permissions
- Check that connection details are correct

**“must be able to SET ROLE” error:**

If you encounter an error like `must be able to SET ROLE "some_role"` when running `CREATE DATABASE ... OWNER ...` or during a restore, this occurs because your current user doesn’t have membership in the role you’re trying to assign as owner.

To resolve this, grant the role to your current user first:

```sql
-- Grant the role to your current user
GRANT some_role TO current_user;

-- Now you can create databases owned by that role
CREATE DATABASE mydb OWNER some_role;
```

Alternatively, if you’re restoring a dump and don’t need to preserve ownership, use the `--no-owner` flag with `pg_restore` or `pg_dump` to skip ownership assignments entirely:

```shellscript
pg_restore --no-owner --no-privileges -h planetscale-host -U planetscale-username -d planetscale-database database_dump.dump
```

**Extension errors:**

- Some PostgreSQL extensions may not be available in PlanetScale Postgres
- Review the [extension compatibility guide](postgres-imports.md#extension-support)

**Large object errors:**

- If using large objects (BLOBs), add `--blobs` flag to pg\_dump

```shellscript
pg_dump --blobs -h source-host -U source-username -d source-database -f database_dump.sql
```

**Timeout errors:**

- For large databases, consider breaking the dump into smaller chunks
- Use custom format with parallel restoration

### Performance Tips:

1. **Use custom format**: Binary format with compression is more efficient
2. **Parallel jobs**: Use `--jobs` parameter for faster restoration
3. **Network considerations**: Ensure stable network connection for large transfers
4. **Disk space**: Monitor available disk space during dump creation

## Schema Considerations

Before migration, review:

## PostgreSQL version compatibility

## Extension support limitations

## Third-party enhancement restrictions

## Next Steps

After successful migration:

For more advanced migration scenarios or larger databases, consider [WAL streaming](postgres-migrate-walstream.md) or [Amazon DMS](postgres-migrate-dms.md) methods.

If you encounter issues during migration, please [reach out to support](https://planetscale.com/contact?initial=support) for assistance.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
