---
url: https://planetscale.com/docs/postgres/imports/supabase
title: "Supabase"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Migrate from Supabase to PlanetScale

> Use this guide to migrate an existing Supabase database to PlanetScale Postgres.

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

<Note>
  Before beginning your migration, we recommend running our [migration assessment tool](https://planetscale.com/liftoff) for instant feedback on migration complexity, potential blockers, and the recommended migration path.
</Note>

Use this guide to migrate an existing Supabase database to PlanetScale Postgres.

This guide will cover a no-downtime approach to migrating using Postgres logical replication. If you are willing to tolerate downtime during a maintenance window, you may also use [`pg_dump` and restore](postgres-migrate-dumprestore.md). The `pg_dump`/restore approach is simpler, but is only for applications where downtime is acceptable.

These instructions work for all versions of Postgres that support logical replication (version 10+). If you have an older version you want to bring to PlanetScale, [contact us](https://planetscale.com/contact?initial=support) for guidance.

Before beginning a migration, you should check our [extensions documentation](../extensions.md) to ensure that all of the extensions you rely on will work on PlanetScale.

As an alternative to this guide, you can also try our [Postgres migration scripts](https://github.com/planetscale/migration-scripts/tree/main/postgres-direct). These allow you to automate some of the manual steps that we describe in this guide.

<Note>
  Want expert guidance for your migration? PlanetScale's [migration services](https://planetscale.com/migrate) are available to help you plan and execute a smooth, successful move.
</Note>

## 1. Prepare your PlanetScale database

Create a new database in the [PlanetScale dashboard](https://app.planetscale.com) or using the [PlanetScale CLI](../../cli.md). A few things to check when configuring your database:

* Ensure you select the correct cloud region. You typically want to use the same region that you deploy your other application infrastructure to.
* Since Supabase uses Postgres, you'll also want to create a Postgres database in PlanetScale.
* Choose the best storage option for your needs. For applications needing high-performance and low-latency I/O, use [PlanetScale Metal](../../metal.md). For applications that need more flexible storage options or smaller compute instances, choose "Elastic Block Storage" or "Persistent Disk."
* Choose between aarch64 and x86-64 architecture. If you don't know which to choose, `aarch64` is a good default choice.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=ed12dab5fee65e01a22bc3bf8b7e3e04" alt="Create a new PlanetScale Postgres database" width="2726" height="2148" data-path="images/assets/docs/postgres/imports/image.png" />
</Frame>

Once the database is created and ready, navigate to your dashboard and click the "Connect" button.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/migration-dashboard-connect.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=9bc8cdd874472873d94478ac18cb08b6" alt="Connect to a PlanetScale Postgres database" width="3688" height="1522" data-path="images/assets/docs/postgres/imports/migration-dashboard-connect.png" />
</Frame>

From here, follow the instructions to create a new default role. This role will act as your admin role, with the highest level of privileges.

Though you may use this one for your migration, we recommend you use a separate role with lesser privileges for your migration and general database connections.

To create a new role, navigate to the [Role management page](../connecting/roles.md). Click "New role" and give the role a memorable name. By default, `pg_read_all_data` and `pg_write_all_data` are enabled. In addition to these, enable `pg_create_subscription` and `postgres`, and then create the role.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image3.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=b6592510d75100ab2e87208d2aacec2e" alt="New Postgres role privileges" width="1882" height="2296" data-path="images/assets/docs/postgres/imports/image3.png" />
</Frame>

Copy the password and all other connection credentials into environment variables for later use:

```
PLANETSCALE_USERNAME=pscale_api_XXXXXXXXXX.XXXXXXXXXX
PLANETSCALE_PASSWORD=pscale_pw_XXXXXXXXXXXXXXXXXXXXXXX
PLANETSCALE_HOST=XXXX.pg.psdb.cloud
PLANETSCALE_DBNAME=postgres
```

We also recommend that you increase `max_worker_processes` for the duration of the migration in order to speed up data copying. Go to the "Parameters" tab of the "Clusters" page:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image4.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=314a03ba73e5cdf908e0ccedb0123e95" alt="Configure parameters" width="3318" height="1964" data-path="images/assets/docs/postgres/imports/image4.png" />
</Frame>

On this page, increase this value from the default of `4` to `10` or more:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image5.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=d29190a32bd982451a5b52ecd2211097" alt="Configure max worker processes" width="1784" height="1152" data-path="images/assets/docs/postgres/imports/image5.png" />
</Frame>

You can decrease these values after the migration is complete.

## 2. Configure disk size on PlanetScale

If you are importing into a database backed by network-attached storage, you must configure your disk in advance to ensure your database will fit.
Though we support disk autoscaling for these, AWS and GCP limit how frequently disks can be resized.
If you don't ensure your disk is large enough for the import in advance, it will not be able to resize fast enough for a large data import.

To configure this, navigate to "Clusters" and then the "Storage" tab:

<img src="https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/imports/storage-configuration-min-size.png?fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=41d36935a4bf7fc12799794d0cde1a51" alt="Storage configuration min size" width="3076" height="2336" data-path="postgres/imports/storage-configuration-min-size.png" />

On this page, adjust the "Minimum disk size."
You should set this value to at least 150% of the size of the database you are migrating.
For example, if the database you are importing is 330 GB, you should set your minimum disk size to at least 500 GB.

The 50% overhead is to account for:

1. Data growth during the import process and
2. Table and index bloat that can occur during the import process.
   This can be later mitigated with careful [VACUUMing](https://www.postgresql.org/docs/current/sql-vacuum.html) or using an extension like [pg\_squeeze](../extensions/pg_squeeze.md), but is difficult to avoid during the migration itself.

When ready, queue and apply the changes.
You can check the "Changes" tab to see the status of the resize:

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/imports/confirm-disk-size-change.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=3642cee5a9eddb534263ecd8a70d81eb" alt="Confirm disk size change" width="2626" height="1264" data-path="postgres/imports/confirm-disk-size-change.png" />

Wait for it to indicate completion.

If you are importing to a Metal database, you must choose a disk size when first creating your database.
You should launch your cluster with a disk size at least 50% larger than the storage used by your current source database (150% of the existing total).

As an example, if you need to import a 330 GB database onto a PlanetScale `M-160`, there are three storage sizes available:

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/imports/metal-disk-size.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=18c7b2f2b9dbdfe9d20b22fbd3e634c7" alt="Metal disk size" width="2074" height="1812" data-path="postgres/imports/metal-disk-size.png" />

You should use the largest, 1.25TB option during the import.
After importing and cleaning up table bloat, you may be able to downsize to the 468 GB option.

## 3. Enable IPv4 direct connections in Supabase

In Supabase, logical replication to external sources requires direct connections. Direct IPv4 connections are not enabled by default. If you have not enabled them yet, go to your project dashboard in Supabase and click the "Connect" button:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image6.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=3457b2e1f89ee76070960a7eb6927c4e" alt="Supabase dashboard" width="2760" height="1300" data-path="images/assets/docs/postgres/imports/image6.png" />
</Frame>

In the connection modal, click "IPv4 add-on."

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image7.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=0479c0d05e9c16e1b352d345891e77b2" alt="Supabase direct" width="3448" height="1660" data-path="images/assets/docs/postgres/imports/image7.png" />
</Frame>

In the menu that appears, enable the IPv4 add-on:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/postgres/imports/image8.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=da38d6f1b5729ac4371dd2aced55c9e8" alt="Supabase IPV4" width="2006" height="1660" data-path="images/assets/docs/postgres/imports/image8.png" />
</Frame>

Supabase notes that enabling this might incur downtime. Take that into account when planning your migration.

Logical replication requires a direct connection to the primary Postgres instance. Use direct connection credentials (not pooled connection credentials) for the rest of this guide.

<Warning>
  Use direct connection host and port `5432` for `pg_dump`, `CREATE PUBLICATION`, and `CREATE SUBSCRIPTION`. Supabase pooled endpoints are not suitable for logical replication.
</Warning>

## 4. Copy schema from Supabase to PlanetScale

Before we begin migrating data, we first must copy the schema from Supabase to PlanetScale. We do this as a distinct set of steps using `pg_dump`.

<Warning>
  You should not make any schema changes during the migration process. You may continue to select, insert, update, and delete data, keeping your application fully online during this process.
</Warning>

For these instructions, you'll need to connect to your Supabase role that has permissions to create replication publications and read all data. You also must use a direct IPv4 connection. Your default role that was generated by Supabase when you first created your database should suffice here. We will assume that the credentials for this user and other connection info are stored in the following environment variables.

```
SUPABASE_USERNAME=XXXX
SUPABASE_PASSWORD=XXXX
SUPABASE_HOST=XXX
SUPABASE_PORT=5432
SUPABASE_DBNAME=XXX
```

Run the below command to take a snapshot of the full schema of the `$SUPABASE_DBNAME` that you want to migrate:

```
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

<Note>
  The above command will dump the tables only for the `public` schema. If you want to include other schemas in the migration, you can repeat these steps for each, or customize the commands to dump multiple schemas at once.
</Note>

The schema then needs to be loaded into your new PlanetScale database:

```bash theme={null}
PGPASSWORD=$PLANETSCALE_PASSWORD \
psql -h $PLANETSCALE_HOST \
     -p 5432 \
     -U $PLANETSCALE_USERNAME \
     -d $PLANETSCALE_DBNAME \
     -f schema.sql
```

In the output of this command, you might see some error messages of the form:

```bash theme={null}
psql:schema.sql:LINE: ERROR: DESCRIPTION
```

You should inspect these to see if they are of any concern. You can [reach out to our support](https://planetscale.com/contact?initial=support) if you need assistance at this step.

## 5. Set up logical replication

We now must create a `PUBLICATION` on Supabase that the PlanetScale database can subscribe to for data copying and replication. This example shows how to create a publication that only publishes changes to tables in the `public` schema of your Postgres database. You can adjust the commands if you want to do so for a different schema, or have multiple schemas to migrate.

First, run this command on your Supabase database to get all of the tables in the `public` schema:

```sql theme={null}
SELECT 'CREATE PUBLICATION replicate_to_planetscale FOR TABLE ' ||
       string_agg(format('%I.%I', schemaname, tablename), ', ') || ';'
FROM pg_tables
WHERE schemaname = 'public';
```

This will generate a query that looks like this:

```sql theme={null}
CREATE PUBLICATION replicate_to_planetscale FOR TABLE
  public.table_1,
  public.table_2,
  ...
  public.table_n;
```

Take this command and execute it on your Supabase database. You should see this if it created correctly:

```sql theme={null}
CREATE PUBLICATION
```

We then need to tell PlanetScale to `SUBSCRIBE` to this publication.

```sql theme={null}
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

**Initial table sync (copy phase)**
PostgreSQL spawns tablesync workers on the PlanetScale side. Each worker opens a replication connection to Supabase and copies one table at a time using a consistent snapshot taken at subscription creation. Up to `max_sync_workers_per_subscription` tables are copied in parallel (the default is 2; we recommend increasing `max_worker_processes` as described above to allow more parallelism).

Because your schema was loaded before the subscription was created, all indexes are live during this phase. Expect elevated CPU on PlanetScale for the duration — this is normal. The larger and more heavily indexed your tables, the longer this phase takes.

**Steady-state replication (streaming phase)**
Once all tables are copied, the tablesync workers exit and a single apply worker takes over, streaming WAL changes from Supabase in real time. CPU usage will drop significantly at this point. This is the state you want to reach and maintain until cutover.

#### Tracking table sync progress

Run this on your PlanetScale database to see the sync state of each table:

```sql theme={null}
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

```sql theme={null}
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

```sql theme={null}
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

```sql theme={null}
SELECT pg_current_wal_lsn();
```

Compare `received_lsn` from PlanetScale against `pg_current_wal_lsn()` from Supabase. During the initial copy phase, these values will diverge — this is expected. Once all tables are in the `ready` state, they should converge quickly. When both values match, the subscriber is fully caught up with the source.

#### Troubleshooting

**CPU is elevated on PlanetScale**
This is expected during the copy phase. Each tablesync worker is writing rows and maintaining indexes simultaneously. CPU will return to normal once all tables reach the `ready` state.

**Rows are not appearing on PlanetScale**
Check that tablesync workers are active:

```sql theme={null}
SELECT pid, backend_type, state
FROM pg_stat_activity
WHERE backend_type LIKE '%worker%';
```

If no workers are running, verify that `max_worker_processes` is high enough to accommodate the subscription workers plus any other background processes (autovacuum, etc.).

**A table is stuck in the `copying` or `catching up` state**
Check for locks on the PlanetScale side that may be blocking the tablesync worker:

```sql theme={null}
SELECT pid, wait_event_type, wait_event, query
FROM pg_stat_activity
WHERE wait_event IS NOT NULL
  AND backend_type LIKE '%worker%';
```

**Replication lag is growing after the copy phase**
If `received_lsn` is falling further behind `pg_current_wal_lsn()` during steady-state replication, your source may be generating changes faster than the single apply worker can apply them. This is uncommon for typical workloads but can occur with very high write volume. Contact [PlanetScale support](https://planetscale.com/contact?initial=support) if you observe this.

## 6. Handling sequences

Logical replication is great at migrating all of your data over to PlanetScale. However, logical replication does *not* synchronize the `nextval` values for [sequences](https://www.postgresql.org/docs/current/sql-createsequence.html) in your database. Sequences are often used for things like auto incrementing IDs, so it's important to ensure we update this before you switch your traffic to PlanetScale.

You can see all of the sequences and their corresponding `nextval`s on your source Supabase database using this command:

```sql theme={null}
SELECT schemaname, sequencename, last_value + increment_by
  AS next_value
  FROM pg_sequences;
```

An example output from this command:

```
 schemaname |   sequencename   | next_value
------------+------------------+------------
 public     | users_id_seq     |        105
 public     | posts_id_seq     |       1417
 public     | followers_id_seq |       3014
```

What this means is that we have three sequences in our database. In this case, they are all being used for auto-incrementing primary keys. The `nextval` for the `users_id_seq` is 105, the `nextval` for the `posts_id_seq` is 1417, and the `nextval` for the `followers_id_seq` is 3014. If you run the same query on your new PlanetScale database, you'll see something like:

```
 schemaname |   sequencename   | next_value
------------+------------------+------------
 public     | users_id_seq     |          0
 public     | posts_id_seq     |          0
 public     | followers_id_seq |          0
```

If you switch traffic over to PlanetScale in this state, you'll likely encounter errors when inserting new rows:

```bash theme={null}
ERROR:  duplicate key value violates unique constraint "XXXX"
DETAIL:  Key (id)=(ZZZZ) already exists.
```

Before switching over, you need to progress all of these sequences forward so that the `nextval`s produced will be greater than any of the values previously produced on the source Supabase database, avoiding constraint violations. There are several approaches you can take for this. A simple way to solve the problem is to first run this query on your source Supabase database:

```sql theme={null}
SELECT 'SELECT setval(''' || schemaname || '.' || sequencename || ''', '
       || (last_value + 10000) || ');' AS query
FROM pg_sequences;
```

This will generate a sequence of queries that will advance the `nextval` by 10,000 for each sequence:

```sql theme={null}
                      query
--------------------------------------------------
 SELECT setval('public.users_id_seq', 10104);
 SELECT setval('public.posts_id_seq', 11416);
 SELECT setval('public.followers_id_seq', 13013);
```

You would then execute these on your target PlanetScale database. You need to ensure you advance each sequence far enough forward so that the sequences in the Supabase database will not reach these `nextval`s before you switch your primary to PlanetScale. For tables that have a high insertion rate, you might need to increase this by a larger value (say, 100,000 or 1,000,000).

## 7. Cutting over to PlanetScale

Before cutting over, confirm that replication is fully caught up by [checking replication lag](#checking-replication-lag). The `received_lsn` on PlanetScale should match `pg_current_wal_lsn()` on Supabase. If they do not match, the PlanetScale database has not yet applied all changes from Supabase — wait for the values to converge before proceeding.

Once replication is caught up, update your application's database connection credentials to point to PlanetScale and deploy.

After doing this, new rows written to PlanetScale will not be reverse-replicated to Supabase. Thus, it's important to ensure you are fully ready for the cutover at this point.

Once this is complete, PlanetScale is now your primary database!

We recommend you keep the old Supabase database around for a few days, in case you discover any data or schemas you forgot to copy over to PlanetScale. If necessary, you can switch traffic back to the old database. However, keep in mind that any database writes that happened with PlanetScale as the primary will not appear on Supabase. This is why it's good to test the database thoroughly before performing the cutover.

## 8. Post-cutover cleanup (optional)

After confirming your application is fully running on PlanetScale, you can clean up logical replication resources:

1. On PlanetScale, drop the subscription:

```sql theme={null}
DROP SUBSCRIPTION IF EXISTS replicate_from_supabase;
```

2. On Supabase, drop the publication:

```sql theme={null}
DROP PUBLICATION IF EXISTS replicate_to_planetscale;
```

If you no longer need direct external connections from Supabase, you can also disable the IPv4 add-on from the Supabase dashboard.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
