---
url: https://planetscale.com/docs/postgres/imports/supabase
title: "Supabase"
description: ""
access_date: 2026-08-07T16:16:03.743Z
current_date: 2026-08-07T16:16:03.743Z
---

Before beginning your migration, we recommend running our [migration assessment tool](https://planetscale.com/liftoff) for instant feedback on migration complexity, potential blockers, and the recommended migration path.

Use this guide to migrate an existing Supabase database to PlanetScale Postgres.

This guide will cover a no-downtime approach to migrating using Postgres logical replication. If you are willing to tolerate downtime during a maintenance window, you may also use [`pg_dump` and restore](postgres-migrate-dumprestore.md). The `pg_dump` /restore approach is simpler, but is only for applications where downtime is acceptable.

These instructions work for all versions of Postgres that support logical replication (version 10+). If you have an older version you want to bring to PlanetScale, [contact us](https://planetscale.com/contact?initial=support) for guidance.

Before beginning a migration, you should check our [extensions documentation](../extensions.md) to ensure that all of the extensions you rely on will work on PlanetScale.

As an alternative to this guide, you can also try our [Postgres migration scripts](https://github.com/planetscale/migration-scripts/tree/main/postgres-direct). These allow you to automate some of the manual steps that we describe in this guide.

Want expert guidance for your migration? PlanetScale’s [migration services](https://planetscale.com/migrate) are available to help you plan and execute a smooth, successful move.

## 1\. Prepare your PlanetScale database

Create a new database in the [PlanetScale dashboard](https://app.planetscale.com/) or using the [PlanetScale CLI](../../cli.md). A few things to check when configuring your database:

- Ensure you select the correct cloud region. You typically want to use the same region that you deploy your other application infrastructure to.
- Since Supabase uses Postgres, you’ll also want to create a Postgres database in PlanetScale.
- Choose the best storage option for your needs. For applications needing high-performance and low-latency I/O, use [PlanetScale Metal](../../metal.md). For applications that need more flexible storage options or smaller compute instances, choose “Elastic Block Storage” or “Persistent Disk.”
- Choose between aarch64 and x86-64 architecture. If you don’t know which to choose, `aarch64` is a good default choice.

![Create a new PlanetScale Postgres database](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=d57abfcd9e5c0298b8d0792a34d9d96e)

Create a new PlanetScale Postgres database

Once the database is created and ready, navigate to your dashboard and click the “Connect” button.

![Connect to a PlanetScale Postgres database](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/migration-dashboard-connect.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=ff02c0d20735f7e5533f6399d4758017)

Connect to a PlanetScale Postgres database

From here, follow the instructions to create a new default role. This role will act as your admin role, with the highest level of privileges.

Though you may use this one for your migration, we recommend you use a separate role with lesser privileges for your migration and general database connections.

To create a new role, navigate to the [Role management page](../connecting/roles.md). Click “New role” and give the role a memorable name. By default, `pg_read_all_data` and `pg_write_all_data` are enabled. In addition to these, enable `pg_create_subscription` and `postgres`, and then create the role.

![New Postgres role privileges](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image3.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=85c997f69697d2739e7089dd24df6dba)

New Postgres role privileges

Copy the password and all other connection credentials into environment variables for later use:

```text
PLANETSCALE_USERNAME=pscale_api_XXXXXXXXXX.XXXXXXXXXX
PLANETSCALE_PASSWORD=pscale_pw_XXXXXXXXXXXXXXXXXXXXXXX
PLANETSCALE_HOST=XXXX.pg.psdb.cloud
PLANETSCALE_DBNAME=postgres
```

We also recommend that you increase `max_worker_processes` for the duration of the migration in order to speed up data copying. Go to the “Parameters” tab of the “Clusters” page:

![Configure parameters](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image4.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=8137047b599e82dd67f97e1102a3fc68)

Configure parameters

On this page, increase this value from the default of `4` to `10` or more:

![Configure max worker processes](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image5.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=5ebab1ddd19b9555b76c22a2c6001f43)

Configure max worker processes

You can decrease these values after the migration is complete.

## 2\. Configure disk size on PlanetScale

If you are importing into a database backed by network-attached storage, you must configure your disk in advance to ensure your database will fit. Though we support disk autoscaling for these, AWS and GCP limit how frequently disks can be resized. If you don’t ensure your disk is large enough for the import in advance, it will not be able to resize fast enough for a large data import.

To configure this, navigate to “Clusters” and then the “Storage” tab:

![Storage configuration min size](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/imports/storage-configuration-min-size.png?w=2500&fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=f4b30c296771178d8a8e8d0ed096da58)

Storage configuration min size

On this page, adjust the “Minimum disk size.” You should set this value to at least 150% of the size of the database you are migrating. For example, if the database you are importing is 330 GB, you should set your minimum disk size to at least 500 GB.

The 50% overhead is a baseline to account for:

1. Data growth during the import process
2. Table and index bloat that can occur during the import process

Schemas with many secondary indexes often need more headroom. Each index is built during the copy phase, which increases copy time and temporary disk usage. If your source database has a large number of indexes, provision more than 150% of your source size.

Bloat can be mitigated after import with careful [VACUUMing](https://www.postgresql.org/docs/current/sql-vacuum.html) or an extension like [pg\_squeeze](../extensions/pg_squeeze.md), but is difficult to avoid during the migration itself.

When ready, queue and apply the changes. You can check the “Changes” tab to see the status of the resize:

![Confirm disk size change](https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/imports/confirm-disk-size-change.png?w=2500&fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=5c7afaf953bbd7ec7ac0e82ed220a75d)

Confirm disk size change

Wait for it to indicate completion.

If you are importing to a Metal database, you must choose a disk size when first creating your database. You should launch your cluster with a disk size at least 50% larger than the storage used by your current source database (150% of the existing total).

As an example, if you need to import a 330 GB database onto a PlanetScale `M-160`, there are three storage sizes available:

![Metal disk size](https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/imports/metal-disk-size.png?w=2500&fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=a4bb2ac32cdbcd18ec686875681ab59d)

Metal disk size

You should use the largest, 1.25TB option during the import. After importing and cleaning up table bloat, you may be able to downsize to the 468 GB option.

## 3\. Enable IPv4 direct connections in Supabase

In Supabase, logical replication to external sources requires direct connections. Direct IPv4 connections are not enabled by default. If you have not enabled them yet, go to your project dashboard in Supabase and click the “Connect” button:

![Supabase dashboard](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image6.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=9c405b69896e8fc8b6e1cf413a507f99)

Supabase dashboard

In the connection modal, click “IPv4 add-on.”

![Supabase direct](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image7.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=671be07b48f9ef186476dd332fb610b4)

Supabase direct

In the menu that appears, enable the IPv4 add-on:

![Supabase IPV4](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image8.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=ae096387090e28d6ca7c28654a63e9d4)

Supabase IPV4

Supabase notes that enabling this might incur downtime. Take that into account when planning your migration.

Logical replication requires a direct connection to the primary Postgres instance. Use direct connection credentials (not pooled connection credentials) for the rest of this guide.

Use direct connection host and port `5432` for `pg_dump`, `CREATE PUBLICATION`, and `CREATE SUBSCRIPTION`. Supabase pooled endpoints are not suitable for logical replication.

## 4\. Copy schema from Supabase to PlanetScale

Before we begin migrating data, we first must copy the schema from Supabase to PlanetScale. We do this as a distinct set of steps using `pg_dump`.

You should not make any schema changes during the migration process. You may continue to select, insert, update, and delete data, keeping your application fully online during this process.

For these instructions, you’ll need to connect to your Supabase role that has permissions to create replication publications and read all data. You also must use a direct IPv4 connection. Your default role that was generated by Supabase when you first created your database should suffice here. We will assume that the credentials for this user and other connection info are stored in the following environment variables.

```text
SUPABASE_USERNAME=XXXX
SUPABASE_PASSWORD=XXXX
SUPABASE_HOST=XXX
SUPABASE_PORT=5432
SUPABASE_DBNAME=XXX
```

Run the below command to take a snapshot of the full schema of the `$SUPABASE_DBNAME` that you want to migrate:

```text
PGPASSWORD=$SUPABASE_PASSWORD \
pg_dump -h $SUPABASE_HOST \
        -p $SUPABASE_PORT \
        -U $SUPABASE_USERNAME \
        -d $SUPABASE_DBNAME \
        --schema-only \
        --no-owner \
        --no-privileges \
        --schema=public \
        -f schema.sql
```

This saves the schema into a file named `schema.sql`.

The above command will dump the tables only for the `public` schema. If you want to include other schemas in the migration, you can repeat these steps for each, or customize the commands to dump multiple schemas at once.

The schema then needs to be loaded into your new PlanetScale database:

```shellscript
PGPASSWORD=$PLANETSCALE_PASSWORD \
psql -h $PLANETSCALE_HOST \
     -p 5432 \
     -U $PLANETSCALE_USERNAME \
     -d $PLANETSCALE_DBNAME \
     -f schema.sql
```

In the output of this command, you might see some error messages of the form:

```shellscript
psql:schema.sql:LINE: ERROR: DESCRIPTION
```

You should inspect these to see if they are of any concern. You can [reach out to our support](https://planetscale.com/contact?initial=support) if you need assistance at this step.

## 5\. Set up logical replication

We now must create a `PUBLICATION` on Supabase that the PlanetScale database can subscribe to for data copying and replication. This example shows how to create a publication that only publishes changes to tables in the `public` schema of your Postgres database. You can adjust the commands if you want to do so for a different schema, or have multiple schemas to migrate.

### Configure the Supabase replication role

Before creating the publication, check the `statement_timeout` on the Supabase role used for replication. Supabase roles may have a low default (for example, 10 seconds). During the initial table copy, replication reads full table contents from Supabase. Large rows, such as tables with `jsonb` columns, can exceed this limit.

When statements time out on the source, table copy can fail repeatedly without cleaning up. This can cause WAL and disk usage to grow on Supabase.

On Supabase, disable or raise the timeout for your replication role:

```sql
ALTER ROLE your_supabase_role SET statement_timeout = 0;
```

You can restore the previous value after migration completes.

First, run this command on your Supabase database to get all of the tables in the `public` schema:

```sql
SELECT 'CREATE PUBLICATION replicate_to_planetscale FOR TABLE ' ||
       string_agg(format('%I.%I', schemaname, tablename), ', ') || ';'
FROM pg_tables
WHERE schemaname = 'public';
```

This will generate a query that looks like this:

```sql
CREATE PUBLICATION replicate_to_planetscale FOR TABLE
  public.table_1,
  public.table_2,
  ...
  public.table_n;
```

Take this command and execute it on your Supabase database. You should see this if it created correctly:

```sql
CREATE PUBLICATION
```

We then need to tell PlanetScale to `SUBSCRIBE` to this publication.

```sql
PGPASSWORD=$PLANETSCALE_PASSWORD psql \
  -h $PLANETSCALE_HOST \
  -U $PLANETSCALE_USERNAME \
  -p 5432 $PLANETSCALE_DBNAME \
  -c "
CREATE SUBSCRIPTION replicate_from_supabase
CONNECTION 'host=$SUPABASE_HOST port=$SUPABASE_PORT dbname=$SUPABASE_DBNAME user=$SUPABASE_USERNAME password=$SUPABASE_PASSWORD'
PUBLICATION replicate_to_planetscale WITH (copy_data = true);"
```

Data copying and replication will begin at this point.

### Monitoring your migration

Once the subscription is created, PlanetScale begins copying data from Supabase. This section explains what is happening internally and how to track progress.

#### What happens during the copy

When the subscription is created with `copy_data = true`, PostgreSQL proceeds in two phases:

**Initial table sync (copy phase)** PostgreSQL spawns tablesync workers on the PlanetScale side. Each worker opens a replication connection to Supabase and copies one table at a time using a consistent snapshot taken at subscription creation. Up to `max_sync_workers_per_subscription` tables are copied in parallel (the default is 2; we recommend increasing `max_worker_processes` as described above to allow more parallelism).

Because your schema was loaded before the subscription was created, all indexes are live during this phase. Expect elevated CPU on PlanetScale for the duration — this is normal. The larger and more heavily indexed your tables, the longer this phase takes.

For schemas with many secondary indexes, one approach is to load only primary key and foreign key indexes in the initial `schema.sql`, run the copy, then create remaining indexes after all tables reach the `ready` state. This reduces CPU usage, copy time, and disk overhead during the sync phase.

**Steady-state replication (streaming phase)** Once all tables are copied, the tablesync workers exit and a single apply worker takes over, streaming WAL changes from Supabase in real time. CPU usage will drop significantly at this point. This is the state you want to reach and maintain until cutover.

#### Tracking table sync progress

Run this on your PlanetScale database to see the sync state of each table:

```sql
SELECT
  srrelid::regclass AS table_name,
  CASE srsubstate
    WHEN 'i' THEN 'queued'
    WHEN 'd' THEN 'copying'
    WHEN 's' THEN 'catching up'
    WHEN 'r' THEN 'ready'
  END AS state
FROM pg_subscription_rel
ORDER BY srsubstate, table_name;
```

A summary view:

```sql
SELECT
  CASE srsubstate
    WHEN 'i' THEN 'queued'
    WHEN 'd' THEN 'copying'
    WHEN 's' THEN 'catching up'
    WHEN 'r' THEN 'ready'
  END AS state,
  count(*) AS tables
FROM pg_subscription_rel
GROUP BY srsubstate
ORDER BY srsubstate;
```

Once all tables show `ready`, the initial copy is complete and you are in steady-state replication.

#### Checking replication lag

You can compare Log Sequence Numbers (LSNs) between Supabase and PlanetScale to measure how far behind the subscriber is. This is useful both for monitoring progress during the copy phase and for confirming that replication is fully caught up before [cutting over](#7-cutting-over-to-planetscale).

Run this on PlanetScale to see the last LSN received by the subscription:

```sql
SELECT
  subname,
  received_lsn,
  latest_end_lsn,
  last_msg_send_time,
  last_msg_receipt_time
FROM pg_stat_subscription
WHERE subname = 'replicate_from_supabase';
```

And on Supabase, to see the current WAL position:

```sql
SELECT pg_current_wal_lsn();
```

Compare `received_lsn` from PlanetScale against `pg_current_wal_lsn()` from Supabase. During the initial copy phase, these values will diverge — this is expected. Once all tables are in the `ready` state, they should converge quickly. When both values match, the subscriber is fully caught up with the source.

#### Troubleshooting

**CPU is elevated on PlanetScale** This is expected during the copy phase. Each tablesync worker is writing rows and maintaining indexes simultaneously. CPU will return to normal once all tables reach the `ready` state.

**Rows are not appearing on PlanetScale** Check that tablesync workers are active:

```sql
SELECT pid, backend_type, state
FROM pg_stat_activity
WHERE backend_type LIKE '%worker%';
```

If no workers are running, verify that `max_worker_processes` is high enough to accommodate the subscription workers plus any other background processes (autovacuum, etc.).

**A table is stuck in the `copying` or `catching up` state** Check for locks on the PlanetScale side that may be blocking the tablesync worker:

```sql
SELECT pid, wait_event_type, wait_event, query
FROM pg_stat_activity
WHERE wait_event IS NOT NULL
  AND backend_type LIKE '%worker%';
```

**Replication lag is growing after the copy phase** If `received_lsn` is falling further behind `pg_current_wal_lsn()` during steady-state replication, your source may be generating changes faster than the single apply worker can apply them. This is uncommon for typical workloads but can occur with very high write volume. Contact [PlanetScale support](https://planetscale.com/contact?initial=support) if you observe this.

**Supabase disk usage is growing during migration** A low `statement_timeout` on the Supabase replication role can cause table copy to fail repeatedly on large rows. Failed copy operations can leave replication slots active and allow WAL to accumulate on Supabase. See [Configure the Supabase replication role](#configure-the-supabase-replication-role) and verify that table copy is completing on PlanetScale using the [table sync progress](#tracking-table-sync-progress) queries.

**Copy phase is slow or PlanetScale disk usage is high** Schemas with many secondary indexes take longer to copy and use more disk during import. See [Configure disk size on PlanetScale](#2-configure-disk-size-on-planetscale) for sizing guidance. For heavily indexed schemas, consider deferring non-primary-key and non-foreign-key indexes until after all tables reach the `ready` state.

## 6\. Handling sequences

Logical replication is great at migrating all of your data over to PlanetScale. However, logical replication does *not* synchronize the `nextval` values for [sequences](https://www.postgresql.org/docs/current/sql-createsequence.html) in your database. Sequences are often used for things like auto incrementing IDs, so it’s important to ensure we update this before you switch your traffic to PlanetScale.

You can see all of the sequences and their corresponding `nextval` s on your source Supabase database using this command:

```sql
SELECT schemaname, sequencename, last_value + increment_by
  AS next_value
  FROM pg_sequences;
```

An example output from this command:

```text
schemaname |   sequencename   | next_value
------------+------------------+------------
 public     | users_id_seq     |        105
 public     | posts_id_seq     |       1417
 public     | followers_id_seq |       3014
```

What this means is that we have three sequences in our database. In this case, they are all being used for auto-incrementing primary keys. The `nextval` for the `users_id_seq` is 105, the `nextval` for the `posts_id_seq` is 1417, and the `nextval` for the `followers_id_seq` is 3014. If you run the same query on your new PlanetScale database, you’ll see something like:

```text
schemaname |   sequencename   | next_value
------------+------------------+------------
 public     | users_id_seq     |          0
 public     | posts_id_seq     |          0
 public     | followers_id_seq |          0
```

If you switch traffic over to PlanetScale in this state, you’ll likely encounter errors when inserting new rows:

```shellscript
ERROR:  duplicate key value violates unique constraint "XXXX"
DETAIL:  Key (id)=(ZZZZ) already exists.
```

Before switching over, you need to progress all of these sequences forward so that the `nextval` s produced will be greater than any of the values previously produced on the source Supabase database, avoiding constraint violations. There are several approaches you can take for this. A simple way to solve the problem is to first run this query on your source Supabase database:

```sql
SELECT 'SELECT setval(''' || schemaname || '.' || sequencename || ''', '
       || (last_value + 10000) || ');' AS query
FROM pg_sequences;
```

This will generate a sequence of queries that will advance the `nextval` by 10,000 for each sequence:

```sql
query
--------------------------------------------------
 SELECT setval('public.users_id_seq', 10104);
 SELECT setval('public.posts_id_seq', 11416);
 SELECT setval('public.followers_id_seq', 13013);
```

You would then execute these on your target PlanetScale database. You need to ensure you advance each sequence far enough forward so that the sequences in the Supabase database will not reach these `nextval` s before you switch your primary to PlanetScale. For tables that have a high insertion rate, you might need to increase this by a larger value (say, 100,000 or 1,000,000).

## 7\. Cutting over to PlanetScale

Before cutting over, confirm that replication is fully caught up by [checking replication lag](#checking-replication-lag). The `received_lsn` on PlanetScale should match `pg_current_wal_lsn()` on Supabase. If they do not match, the PlanetScale database has not yet applied all changes from Supabase — wait for the values to converge before proceeding.

Once replication is caught up, update your application’s database connection credentials to point to PlanetScale and deploy.

After doing this, new rows written to PlanetScale will not be reverse-replicated to Supabase. Thus, it’s important to ensure you are fully ready for the cutover at this point.

Once this is complete, PlanetScale is now your primary database!

We recommend you keep the old Supabase database around for a few days, in case you discover any data or schemas you forgot to copy over to PlanetScale. If necessary, you can switch traffic back to the old database. However, keep in mind that any database writes that happened with PlanetScale as the primary will not appear on Supabase. This is why it’s good to test the database thoroughly before performing the cutover.

## 8\. Post-cutover cleanup (optional)

After confirming your application is fully running on PlanetScale, you can clean up logical replication resources:

1. On PlanetScale, drop the subscription:

```sql
DROP SUBSCRIPTION IF EXISTS replicate_from_supabase;
```

2. On Supabase, drop the publication:

```sql
DROP PUBLICATION IF EXISTS replicate_to_planetscale;
```

If you no longer need direct external connections from Supabase, you can also disable the IPv4 add-on from the Supabase dashboard.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
