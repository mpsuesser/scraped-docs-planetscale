---
url: https://planetscale.com/docs/postgres/imports/postgres-migrate-walstream
title: "Postgres Migrate Walstream"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

Before beginning your migration, we recommend running our [migration assessment tool](https://planetscale.com/liftoff) for instant feedback on migration complexity, potential blockers, and the recommended migration path.

[Write-Ahead Logging (WAL)](https://www.postgresql.org/docs/current/wal-intro.html) streaming provides a near-zero downtime migration method by continuously replicating changes from your source PostgreSQL database to PlanetScale Postgres using [logical replication](https://www.postgresql.org/docs/current/logical-replication.html).

Want expert guidance for your migration? PlanetScale’s [migration services](https://planetscale.com/migrate) are available to help you plan and execute a smooth, successful move.

## Overview

This migration method involves:

This method requires administrative access to your source PostgreSQL database to configure replication settings.

## Prerequisites

Before starting the migration:

- PostgreSQL 10+ on the source database (logical replication support)
- Administrative access to source database configuration
- Network connectivity between source and PlanetScale Postgres
- Connection details for your PlanetScale Postgres database from the console
- Ensure the disk on your PlanetScale database has at least 150% of the capacity of your source database. If you are migrating to a PlanetScale database backed by network-attached storage, you can [resize](../cluster-configuration/cluster-storage.md) your disk manually by setting the “Minimum disk size.” If you are using Metal, you will need to select a size when first creating your database. For example, if your source database is 330GB, you should have at least 500GB of storage available on PlanetScale.
- Understanding of your application’s write patterns for cutover planning

## Step 1: Configure Source Database

### Enable logical replication on source database:

Edit your PostgreSQL configuration (`postgresql.conf`):

```ini
# Enable logical replication
wal_level = logical

# Set maximum replication slots
max_replication_slots = 10

# Set maximum WAL senders
max_wal_senders = 10

# Enable logical replication workers
max_logical_replication_workers = 10
```

### Configure authentication (pg\_hba.conf):

Add an entry to allow replication connections:

```ini
# Allow replication connections
host replication replication_user source_ip/32 md5
```

### Restart PostgreSQL service:

```shellscript
# On systemd systems
sudo systemctl restart postgresql

# On older systems
sudo service postgresql restart
```

## Step 2: Create Replication User

Connect to your source database and create a replication user:

```sql
-- Create replication user
CREATE USER replication_user WITH REPLICATION LOGIN;

-- Grant necessary permissions
GRANT CONNECT ON DATABASE your_database TO replication_user;
GRANT USAGE ON SCHEMA public TO replication_user;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO replication_user;

-- Grant permissions for future tables
ALTER DEFAULT PRIVILEGES IN SCHEMA public
    GRANT SELECT ON TABLES TO replication_user;
```

## Step 3: Create Publication on Source

Create a publication for the tables you want to replicate:

```sql
-- Create publication for all tables
CREATE PUBLICATION planetscale_migration FOR ALL TABLES;

-- Or create publication for specific tables
CREATE PUBLICATION planetscale_migration FOR TABLE
    table1, table2, table3;

-- Verify publication
SELECT * FROM pg_publication;
```

## Step 4: Get PlanetScale Connection Details

From your PlanetScale console:

## Step 5: Create Initial Schema on PlanetScale

Export and import the schema structure:

```shellscript
# Export schema from source
pg_dump -h source-host \
        -p source-port \
        -U source-username \
        -d source-database \
        --schema-only \
        --no-owner \
        --no-privileges \
        -f schema.sql

# Import schema to PlanetScale
psql -h planetscale-host \
     -p planetscale-port \
     -U planetscale-username \
     -d planetscale-database \
     -f schema.sql
```

## Step 6: Create replication slot and capture snapshot

Before exporting data, create the replication slot on the source database. This captures a consistent snapshot you will use for export so the dump and replication start point stay aligned with no gaps or duplicates.

```sql
-- Run on source database in a dedicated session
-- This creates the slot and returns a snapshot name
SELECT slot_name, snapshot_name, confirmed_flush_lsn
FROM pg_create_logical_replication_slot('planetscale_slot', 'pgoutput');
```

Record the `snapshot_name` value from the output and use it in the next step.

Keep the source database session open after running this command. The snapshot is valid only while the creating session remains open. If the session closes, drop the slot and repeat this step to get a new snapshot.

## Step 7: Export and import initial data

Use the snapshot captured in Step 6 when running `pg_dump` so the data export matches the replication slot start position:

```shellscript
# Export data using the consistent snapshot from Step 6
pg_dump -h source-host \
        -p source-port \
        -U source-username \
        -d source-database \
        --data-only \
        --no-owner \
        --no-privileges \
        --snapshot=<snapshot_name_from_step_6> \
        -f data.sql

# Import data to PlanetScale
psql -h planetscale-host \
     -p planetscale-port \
     -U planetscale-username \
     -d planetscale-database \
     -f data.sql
```

After import completes, close the source session that was holding the snapshot.

## Step 8: Set up logical replication

Create the subscription on PlanetScale referencing the existing slot from Step 6:

Always include `copy_data = false` when creating a subscription after a manual data import. Without it, PostgreSQL attempts to copy table data again and can trigger duplicate key errors.

```sql
-- Create subscription referencing the pre-created slot
CREATE SUBSCRIPTION planetscale_subscription
    CONNECTION 'host=source-host port=source-port
                dbname=source-database user=replication_user
                password=replication-password'
    PUBLICATION planetscale_migration
    WITH (
        copy_data = false,
        create_slot = false,
        slot_name = 'planetscale_slot'
    );

-- Verify subscription is active
SELECT subname, subenabled, subslotname FROM pg_subscription;

-- Confirm replication is streaming
SELECT subname, received_lsn, latest_end_lsn, latest_end_time
FROM pg_stat_subscription;
```

## Step 9: Monitor replication

### Check replication lag:

```sql
-- On source database
SELECT * FROM pg_replication_slots;

-- On PlanetScale database
SELECT
    subname,
    received_lsn,
    latest_end_lsn,
    latest_end_time
FROM pg_stat_subscription;
```

### Monitor for conflicts:

```sql
-- Check for subscription errors
SELECT * FROM pg_stat_subscription
WHERE last_msg_failure_time IS NOT NULL;
```

## Step 10: Prepare for cutover

### Verify data consistency:

```sql
-- Compare row counts between source and target
-- Run on both databases
SELECT
    schemaname,
    tablename,
    n_tup_ins as estimated_rows
FROM pg_stat_user_tables
ORDER BY schemaname, tablename;
```

### Check replication lag:

Ensure replication lag is minimal (ideally under 1 second) before cutover.

## Step 11: Handle sequences

Logical replication synchronizes table data, but it does not synchronize the `nextval` values for [sequences](https://www.postgresql.org/docs/current/sql-createsequence.html) in your database. Sequences are often used for auto-incrementing IDs, so update them on PlanetScale before switching application traffic.

You can see all sequences and their corresponding `nextval` values on your source database using this command:

```sql
SELECT schemaname, sequencename, last_value + increment_by
  AS next_value
  FROM pg_sequences;
```

Example output:

```text
schemaname |   sequencename   | next_value
------------+------------------+------------
 public     | users_id_seq     |        105
 public     | posts_id_seq     |       1417
 public     | followers_id_seq |       3014
```

In this example, the database has three sequences used for auto-incrementing primary keys. The `nextval` for `users_id_seq` is 105, the `nextval` for `posts_id_seq` is 1417, and the `nextval` for `followers_id_seq` is 3014. If you run the same query on your PlanetScale database, the sequence values may be behind the source database.

If you switch traffic to PlanetScale while sequences are behind, inserts can fail with duplicate key errors:

```shellscript
ERROR:  duplicate key value violates unique constraint "XXXX"
DETAIL:  Key (id)=(ZZZZ) already exists.
```

Before switching over, advance these sequences so the `nextval` values produced on PlanetScale are greater than any values previously produced on the source database. A simple approach is to run this query on your source database:

```sql
SELECT 'SELECT setval(''' || schemaname || '.' || sequencename || ''', '
       || (last_value + 10000) || ');' AS query
FROM pg_sequences;
```

This generates queries that advance each sequence by 10,000:

```sql
query
--------------------------------------------------
 SELECT setval('public.users_id_seq', 10104);
 SELECT setval('public.posts_id_seq', 11416);
 SELECT setval('public.followers_id_seq', 13013);
```

Execute the generated queries on your PlanetScale database. Advance each sequence far enough that the source database will not reach those `nextval` values before you switch your primary database to PlanetScale. For tables with a high insertion rate, increase the offset to a larger value, such as 100,000 or 1,000,000.

## Step 12: Perform cutover

When ready to switch to PlanetScale Postgres:

### Verify cutover success:

```sql
-- Check that latest data is present
SELECT count(*), max(updated_at) FROM your_main_table;
```

## Step 13: Cleanup (after successful cutover)

### Drop subscription on PlanetScale:

```sql
DROP SUBSCRIPTION planetscale_subscription;
```

### Drop publication on source:

```sql
DROP PUBLICATION planetscale_migration;
```

## Troubleshooting

### Common Issues:

**Replication slot conflicts:**

```sql
-- Check existing slots
SELECT * FROM pg_replication_slots;

-- Drop unused slots
SELECT pg_drop_replication_slot('slot_name');
```

**Permission errors:**

- Verify replication user has correct permissions
- Check pg\_hba.conf configuration
- Ensure network connectivity

**Large transaction delays:**

- Monitor for long-running transactions on source
- Consider breaking large operations into smaller batches

**Subscription conflicts:**

```sql
-- Check subscription worker status
SELECT * FROM pg_stat_subscription;

-- Restart subscription if needed
ALTER SUBSCRIPTION planetscale_subscription DISABLE;
ALTER SUBSCRIPTION planetscale_subscription ENABLE;
```

**Duplicate key errors immediately after subscription creation:**

The subscription was likely created without `copy_data = false` after manual data import. Drop the subscription immediately to stop retry loops:

```sql
DROP SUBSCRIPTION planetscale_subscription;
```

Clean up leaked replication origins:

```sql
DO $$
DECLARE r RECORD;
BEGIN
  FOR r IN
    SELECT roname FROM pg_replication_origin
    WHERE roname NOT IN (
      SELECT 'pg_' || oid::text FROM pg_subscription
    )
  LOOP
    PERFORM pg_replication_origin_drop(r.roname);
  END LOOP;
END;
$$;
```

Then drop the replication slot on the source, repeat Steps 6-8 with a fresh snapshot, and recreate the subscription with `copy_data = false`.

**`could not find free replication state slot` errors:**

This is commonly caused by the duplicate key crash loop above. Each unclean worker exit can leak a replication origin until `max_active_replication_origins` is exhausted. Resolve the duplicate key issue first and run the origin cleanup. If the error persists, contact PlanetScale support.

## Performance Considerations

1. **Network bandwidth**: Ensure sufficient bandwidth for initial sync and ongoing replication
2. **Disk I/O**: Monitor disk usage on both source and target during replication
3. **Replication lag**: Keep lag minimal by optimizing source database performance
4. **Conflict resolution**: Understand how PostgreSQL handles replication conflicts

## Schema Considerations

Before migration, review:

## PostgreSQL version compatibility

## Extension support limitations

## Third-party enhancement restrictions

## Next Steps

After successful migration:

For simpler migrations or if you don’t have administrative access to your source database, consider the [pg\_dump/restore method](postgres-migrate-dumprestore.md). For more complex scenarios, explore [Amazon DMS](postgres-migrate-dms.md).

If you encounter issues during migration, please [reach out to support](https://planetscale.com/contact?initial=support) for assistance.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
