---
url: https://planetscale.com/docs/postgres/imports/neon
title: "Neon"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

Before beginning your migration, we recommend running our [migration assessment tool](https://planetscale.com/liftoff) for instant feedback on migration complexity, potential blockers, and the recommended migration path.

This guide will cover a no-downtime approach to migrating using Postgres logical replication. If you are willing to tolerate downtime during a maintenance window, you may also use [`pg_dump` and restore](postgres-migrate-dumprestore.md). The `pg_dump` /restore approach is simpler, but is only for applications where downtime is acceptable.

These instructions work for all versions of Postgres that support logical replication (version 10+). If you have an older version you want to bring to PlanetScale, [contact us](https://planetscale.com/contact?initial=support) for guidance.

Before beginning a migration, you should check our [extensions documentation](../extensions.md) to ensure that all of the extensions you rely on will work on PlanetScale.

As an alternative to this guide, you can also try our [Postgres migration scripts](https://github.com/planetscale/migration-scripts/tree/main/postgres-direct). These allow you to automate some of the manual steps that we describe in this guide.

Want expert guidance for your migration? PlanetScale’s [migration services](https://planetscale.com/migrate) are available to help you plan and execute a smooth, successful move.

## 1\. Prepare your PlanetScale database

Create a new database in the [PlanetScale dashboard](https://app.planetscale.com/) or using the [PlanetScale CLI](../../cli.md). A few things to check when configuring your database:

- Ensure you select the correct cloud region. You typically want to use the same region that you deploy your other application infrastructure to.
- This guide assumes you are migrating from a Neon database, so also choose the Postgres option in PlanetScale.
- Choose the best storage option for your needs. For applications needing high-performance and low-latency I/O, use [PlanetScale Metal](../../metal.md). For applications that need more flexible storage options or smaller compute instances, choose “Elastic Block Storage” or “Persistent Disk.”

![Create a new PlanetScale Postgres database](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/neon/image.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=14b7879471576772f73188e9eb6de428)

Create a new PlanetScale Postgres database

Once the database is created and ready, navigate to your dashboard and click the “Connect” button.

![Connect to a PlanetScale Postgres database](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/neon/image2.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=e0b93dea71e300b4c90d09d083079637)

Connect to a PlanetScale Postgres database

From here, follow the instructions to create a new default role. This role will act as your admin role, with the highest level of privileges.

Though you may use this one for your migration, we recommend you use a separate role with lesser privileges for your migration and general database connections.

To create a new role, navigate to the [Role management page](../connecting/roles.md) in your database settings. Click “New role” and give the role a memorable name. By default, `pg_read_all_data` and `pg_write_all_data` are enabled. In addition to these, enable `pg_create_subscription` and `postgres`, and then create the role.

![New Postgres role privileges](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/neon/image3.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=366687049d96e06fe0c1698a8ae6bc05)

New Postgres role privileges

Copy the password and all other connection credentials into environment variables for later use:

```shellscript
PLANETSCALE_USERNAME=pscale_api_XXXXXXXXXX.XXXXXXXXXX
PLANETSCALE_PASSWORD=pscale_pw_XXXXXXXXXXXXXXXXXXXXXXX
PLANETSCALE_HOST=XXXX.pg.psdb.cloud
PLANETSCALE_DBNAME=postgres
```

We also recommend that you increase `max_worker_processes` for the duration of the migration, in order to speed up data copying. Go to the “Parameters” tab of the “Clusters” page:

![Configure parameters](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/neon/image4.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=e502fd27587a995e215d6f466ac31d95)

Configure parameters

On this page, increase this value from the default of `4` to `10` or more:

![Configure max worker processes](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/neon/image5.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=84cc43801ae8eda2136971faca644a51)

Configure max worker processes

You can decrease these values after the migration is complete.

## 2\. Configure disk size on PlanetScale

If you are importing into a database backed by network-attached storage, you must configure your disk in advance to ensure your database will fit. Though we support disk autoscaling for these, AWS and GCP limit how frequently disks can be resized. If you don’t ensure your disk is large enough for the import in advance, it will not be able to resize fast enough for a large data import.

To configure this, navigate to “Clusters” and then the “Storage” tab:

![Storage configuration min size](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/imports/storage-configuration-min-size.png?w=2500&fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=f4b30c296771178d8a8e8d0ed096da58)

Storage configuration min size

On this page, adjust the “Minimum disk size.” You should set this value to at least 150% of the size of the database you are migrating. For example, if the database you are importing is 330 GB, you should set your minimum disk size to at least 500 GB.

The 50% overhead is to account for:

1. Data growth during the import process and
2. Table and index bloat that can occur during the import process. This can be later mitigated with careful [VACUUMing](https://www.postgresql.org/docs/current/sql-vacuum.html) or using an extension like [pg\_squeeze](../extensions/pg_squeeze.md), but is difficult to avoid during the migration itself.

When ready, queue and apply the changes. You can check the “Changes” tab to see the status of the resize:

![Confirm disk size change](https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/imports/confirm-disk-size-change.png?w=2500&fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=5c7afaf953bbd7ec7ac0e82ed220a75d)

Confirm disk size change

Wait for it to indicate completion.

If you are importing to a Metal database, you must choose a disk size when first creating your database. You should launch your cluster with a disk size at least 50% larger than the storage used by your current source database (150% of the existing total).

As an example, if you need to import a 330 GB database onto a PlanetScale `M-160` there are three storage sizes available:

![Metal disk size](https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/imports/metal-disk-size.png?w=2500&fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=a4bb2ac32cdbcd18ec686875681ab59d)

Metal disk size

You should use the largest, 1.25TB option during the import. After importing and cleaning up table bloat, you may be able to downsize to the 468 GB option. Resizing is a no-downtime operation that can be performed on the [clusters](../cluster-configuration.md) page.

## 3\. Prepare the Neon database

You will need to enable logical replication to use this import guide. To do so, go to the settings page in your Neon project:

![Neon dashboard](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/imports/neon-dashboard-darkmode.png?w=2500&fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=c9d8a9739be1f83bd702704d20619216)

Neon dashboard

Navigate to the “Logical Replication” section and ensure it is enabled.

Note the warnings in the Neon dashboard. Changing this setting will sever all existing connections and restart all computes.

![Neon logical replication](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/imports/neon-logical-replication-darkmode.png?w=2500&fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=cc1331752310903103b76a982e4535b4)

Neon logical replication

You can confirm that the `wal_level` is set to `logical` by running `SHOW wal_level;` on your Neon database:

```sql
neondb=> SHOW wal_level;
 wal_level
-----------
 logical
```

If you see a result other than `logical`, then it is not configured correctly. Once this is enabled, return to the dashboard.

For these instructions, you’ll need to connect to Neon with a role that has permissions to create replication publications and read all data. Your default role that was generated by Neon when you first created your database should suffice here. To get these connection credentials to use for the migration, click on the “Connect” button from your project dashboard:

![Neon dashboard connect](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/imports/neon-dashboard-connect-darkmode.png?w=2500&fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=360c98e210636d5af7582212779abd36)

Neon dashboard connect

In the connection modal that appears, ensure you have “connection pooling” disabled.

![Neon connect](https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/imports/neon-connect-darkmode.png?w=2500&fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=06440671120d1810380b1f806eb62e4c)

Neon connect

The credentials displayed here are the ones you can use for the migration. For the rest of this guide, we’ll assume these are saved into the following environment variables:

```shellscript
NEON_USERNAME=XXXX
NEON_PASSWORD=XXXX
NEON_HOST=XXX
NEON_DBNAME=XXX
```

## 4\. Copy schema from Neon to PlanetScale

Before we begin migrating data, we first must copy the schema from Neon to PlanetScale. We do this as a distinct set of steps using `pg_dump`.

You should not make any schema changes during the migration process. You may continue to select, insert, update, and delete data, keeping your application fully online during this process.

Run the below command to take a snapshot of the schema for the `$NEON_DBNAME` database that you want to migrate:

```shellscript
PGPASSWORD=$NEON_PASSWORD \
pg_dump -h $NEON_HOST \
        -p 5432 \
        -U $NEON_USERNAME \
        -d $NEON_DBNAME \
        --schema-only \
        --no-owner \
        --no-privileges \
        -f schema.sql
```

This saves the schema into a file named `schema.sql`.

The above command will dump the tables for all schemas in the current database. If you want to migrate only one specific schema, you can add the `--schema=SCHEMA_NAME` option.

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

```text
psql:schema.sql:LINE: ERROR: DESCRIPTION
```

You should inspect these to see if they are of any concern. You can [reach out to our support](https://planetscale.com/contact) if you need assistance at this step.

## 5\. Set up logical replication

We now must create a `PUBLICATION` on Neon that the PlanetScale database can subscribe to for data copying and replication.

To create a publication for all tables in all schemas of the current database, connect to the Neon database:

```text
PGPASSWORD=$NEON_PASSWORD \
PGSSLMODE=require \
PGCHANNELBINDING=require \
psql \
  -h $NEON_HOST \
  -U $NEON_USERNAME \
  -p 5432 \
  $NEON_DBNAME
```

Then run the following command:

```sql
CREATE PUBLICATION replicate_to_planetscale FOR ALL TABLES;
```

You should see this output if it created correctly:

```sql
CREATE PUBLICATION
```

To publish changes for only one specific schema, run the following query:

```sql
SELECT 'CREATE PUBLICATION replicate_to_planetscale FOR TABLE ' ||
       string_agg(format('%I.%I', schemaname, tablename), ', ') || ';'
FROM pg_tables
WHERE schemaname = 'YOUR_SCHEMA_NAME';
```

This will generate a query that looks like this:

```sql
CREATE PUBLICATION replicate_to_planetscale FOR TABLE
  public.table_1,
  public.table_2,
  ...
  public.table_n;
```

You can then copy/paste this and execute on Neon. This will create a publication that only publishes changes for the tables in `YOUR_SCHEMA_NAME`

After creating the publication on Neon, we need to tell PlanetScale to `SUBSCRIBE` to this publication.

```sql
PGPASSWORD=$PLANETSCALE_PASSWORD psql \
  -h $PLANETSCALE_HOST \
  -U $PLANETSCALE_USERNAME \
  -p 5432 $PLANETSCALE_DBNAME \
  -c "
CREATE SUBSCRIPTION replicate_from_neon
CONNECTION 'host=$NEON_HOST dbname=$NEON_DBNAME user=$NEON_USERNAME password=$NEON_PASSWORD sslmode=require channel_binding=require'
PUBLICATION replicate_to_planetscale WITH (copy_data = true);"
```

Data copying and replication will begin at this point. To check in on the row counts for the tables, you can run a query like this on your source and target databases:

```sql
SELECT table_name, row_count FROM (
  SELECT 'table_name_1' as table_name, COUNT(*) as row_count FROM table_name_1 UNION ALL
  SELECT 'table_name_2', COUNT(*) FROM table_name_2 UNION ALL
  ...
  SELECT 'table_name_N', COUNT(*) FROM table_name_N
) t ORDER BY table_name;
```

When the row counts match (or nearly match) you can begin testing and prepare for your application to cutover to use PlanetScale.

## 6\. Handling sequences

Logical replication is great at migrating all of your data over to PlanetScale. However, logical replication does *not* synchronize the `nextval` values for [sequences](https://www.postgresql.org/docs/current/sql-createsequence.html) in your database. Sequences are often used for things like auto incrementing IDs, so it’s important to ensure we update this before you switch your traffic to PlanetScale.

You can see all of the sequences and their corresponding `nextval` s on your source Neon database using this command:

```sql
SELECT schemaname, sequencename, last_value + increment_by AS next_value
FROM pg_sequences;
```

An example output from this command:

```sql
schemaname |   sequencename   | next_value
------------+------------------+------------
 public     | users_id_seq     |        105
 public     | posts_id_seq     |       1417
 public     | followers_id_seq |       3014
```

What this means is that we have three sequences in our database. In this case, they are all being used for auto-incrementing primary keys. The `nextval` for the `users_id_seq` is 105, the `nextval` for the `posts_id_seq` is 1417, and the `nextval` for the `followers_id_seq` is 3014. If you run the same query on your new PlanetScale database, you’ll see something like:

```sql
schemaname |   sequencename   | next_value
------------+------------------+------------
 public     | users_id_seq     |          0
 public     | posts_id_seq     |          0
 public     | followers_id_seq |          0
```

If you switch traffic over to PlanetScale in this state, you’ll likely encounter errors when inserting new rows:

```sql
ERROR:  duplicate key value violates unique constraint "XXXX"
DETAIL:  Key (id)=(ZZZZ) already exists.
```

Before switching over, you need to progress all of these sequences forward so that the `nextval` s produced will be greater than any of the values previously produced on the source Neon database, avoiding constraint violations. There are several approaches you can take for this. A simple way to solve the problem is to first run this query on your source Neon database:

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

You would then execute these on your target PlanetScale database. You need to ensure you advance each sequence far enough forward so that the sequences in the Neon database will not reach these `nextval` s before you switch your primary to PlanetScale. For tables that have a high insertion rate, you might need to increase this by a larger value (say, 100,000 or 1,000,000).

## 7\. Cutting over to PlanetScale

Before you cutover, it’s good to have confidence that the replication between Neon and PlanetScale is fully caught up. You can do this using Log Sequence Numbers (LSNs). The goal is to see these match up between the source Neon database and the target PlanetScale database exactly. If they don’t, it indicates that the PlanetScale database is not fully caught-up with the changes happening on Neon.

You can run this on Neon to see the current LSN:

```sql
postgres=> SELECT pg_current_wal_lsn();
 pg_current_wal_lsn
--------------------
 0/703FE460
```

Then on PlanetScale, you would run the following query to check for a match:

```sql
postgres=> SELECT received_lsn, latest_end_lsn
             FROM pg_stat_subscription
             WHERE subname = 'replicate_from_neon';
 received_lsn | latest_end_lsn
--------------+----------------
 0/703FE460   | 0/703FE460
```

Once you are comfortable that all your data has successfully copied over and replication is sufficiently caught up, it’s time to switch to PlanetScale. In your application code, prepare the cutover by changing the database connection credentials to go to PlanetScale rather than Neon. Then, you can deploy this new version of your application, which will begin using PlanetScale as your primary database.

After doing this, new rows written to PlanetScale will not be reverse-replicated to Neon. Thus, it’s important to ensure you are fully ready for the cutover at this point.

Once this is complete, PlanetScale is now your primary database! We recommend you keep your old database around for at least a few days, just in case you discover any data or schemas you forgot to copy over to PlanetScale.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
