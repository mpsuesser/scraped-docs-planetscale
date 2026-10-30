---
url: https://planetscale.com/docs/postgres/integrations/artie
title: "Artie"
description: ""
access_date: 2026-10-30T21:12:08.571Z
current_date: 2026-10-30T21:12:08.571Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Artie integration

> Connect PlanetScale Postgres to Artie so you can stream changes into your data warehouse or lakehouse for analysis, transformation, and more.

This page covers Postgres.

Vitess version: [Vitess](https://planetscale.com/docs/vitess/integrations/artie.md)

Artie is a fully managed change data capture (CDC) platform.
It streams changes from your PlanetScale Postgres database into warehouses and lakehouses such as Snowflake, BigQuery, Databricks, Redshift, and ClickHouse.
This document will walk you through connecting your PlanetScale Postgres database to Artie.

## Connect to Artie

### Requirements

* A PlanetScale Postgres database
* Database or organization administrator access, to change cluster parameters
* Access to your branch's default role
* An Artie account

#### Network access

If your database uses [IP restrictions](https://planetscale.com/docs/postgres/connecting/ip-restrictions.md), add Artie's fixed IP addresses to the allowlist before you connect. IP restrictions are set per database.

To connect over a private network instead, set up [AWS PrivateLink](https://planetscale.com/docs/postgres/connecting/private-connections/aws-privatelink.md) with Artie's PrivateLink guide. Use your branch's **Private Host** (`*.private-pg.psdb.cloud`) as the host when you connect.

### Configure your PlanetScale cluster

#### Set cluster parameters

To enable logical replication, configure these parameters in your branch's **Clusters > Parameters** tab:

| Parameter | Required Value | Description |
| - | - | - |
| `wal_level` | `logical` | Enables logical replication. |
| `max_replication_slots` | 2 x replicas and CDC consumers | Each Artie source reader uses its own slot. Count them alongside your replicas and any other CDC tools. |
| `max_wal_senders` | 2 x replicas and CDC consumers | Not less than `max_replication_slots`. |
| `max_slot_wal_keep_size` | > 4GB | The most WAL a slot can hold back. Tune for your change rate and available disk space. |
| `sync_replication_slots` | `on` | Synchronizes Artie's slot to your replicas so it survives a switchover or failover. |
| `hot_standby_feedback` | `on` | Required alongside `sync_replication_slots`. |

`wal_level`, `max_replication_slots`, and `max_wal_senders` require a restart.
Queue all three changes with "**Queue parameter changes**", then click "**Apply changes**" once so the cluster restarts once.
On production clusters, PlanetScale performs a rolling restart, which terminates direct connections during a brief switchover.

After the cluster restarts, verify the WAL level:

```sql theme={null}
SHOW wal_level;
```

The result should show `logical`.

#### Create a replication user

Connect to the primary as the default role on port `5432` and create a dedicated user for Artie:

```sql theme={null}
CREATE USER artie_transfer WITH REPLICATION PASSWORD 'strong_password';

GRANT USAGE ON SCHEMA public TO artie_transfer;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO artie_transfer;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO artie_transfer;
```

Repeat the grants for each schema you want to replicate.
If another role owns your tables, such as a migration role, run the `GRANT SELECT` and `ALTER DEFAULT PRIVILEGES` statements as that role too.

PlanetScale routes connections by branch, so append your branch ID to log in: `artie_transfer.<branch_id>`.
It's the suffix on your default role's username, such as `postgres.12345678`.
In SQL, use `artie_transfer` without it.

> [!NOTE]
>
> Roles created via SQL do not appear on the **Settings > Roles** page and must be managed directly in Postgres.
> A user-defined role can only have the `REPLICATION` attribute if it inherits `postgres`, so this SQL user is the least-privileged option.

#### Create a heartbeat table (optional)

Artie's replication slot only advances when a published table changes.
If those tables can go hours without writes while the rest of the database keeps writing, the slot holds back WAL.
To prevent this, create a heartbeat table, which Artie updates every 5 minutes.
Skip this if your published tables change regularly.
As the default role, run:

```sql theme={null}
CREATE TABLE test_heartbeat_table (id text PRIMARY KEY, ts timestamp);
GRANT SELECT, UPDATE ON TABLE test_heartbeat_table TO artie_transfer;
INSERT INTO test_heartbeat_table (id, ts) VALUES ('1', NOW());
```

#### Create a publication

Artie only creates the publication itself when its user is a superuser.
The PlanetScale role isn't one, so create it as the default role.
Artie uses a publication named `dbz_publication` unless you pick another one when you connect.

> [!WARNING]
>
> Once a table is in a publication, Postgres rejects `UPDATE` and `DELETE` on it unless the table has a primary key or a replica identity.
> Check your tables before you create the publication.

List the tables that would be affected:

```sql theme={null}
SELECT n.nspname AS table_schema, c.relname AS table_name
FROM pg_class c
JOIN pg_namespace n ON n.oid = c.relnamespace
WHERE c.relkind = 'r'
  AND c.relpersistence = 'p'
  AND n.nspname NOT IN ('pg_catalog', 'information_schema')
  AND n.nspname NOT LIKE 'pg_toast%'
  AND (
    c.relreplident = 'n'
    OR (c.relreplident = 'd' AND NOT EXISTS (
      SELECT 1 FROM pg_index i WHERE i.indrelid = c.oid AND i.indisprimary))
    OR (c.relreplident = 'i' AND NOT EXISTS (
      SELECT 1 FROM pg_index i WHERE i.indrelid = c.oid AND i.indisreplident))
  )
ORDER BY 1, 2;
```

If this returns any tables, set the replica identity to `FULL` on each one, or leave them out of a publication of specific tables:

```sql theme={null}
ALTER TABLE table1 REPLICA IDENTITY FULL;
```

Then create a publication for all tables or for specific tables.

To publish all tables, run:

```sql theme={null}
CREATE PUBLICATION dbz_publication FOR ALL TABLES WITH (publish_via_partition_root = true);
```

This includes tables you create later, so give every new table a primary key or replica identity.
To limit what Artie receives, publish specific tables instead.

To publish specific tables, list them.
If you created a heartbeat table, include it:

```sql theme={null}
CREATE PUBLICATION dbz_publication
  FOR TABLE public.orders, public.customers, test_heartbeat_table
  WITH (publish_via_partition_root = true);
```

Remember to update your publication before adding new tables to your Artie pipeline:

```sql theme={null}
ALTER PUBLICATION dbz_publication ADD TABLE public.refunds;
```

### Set up Artie

1. In PlanetScale, navigate to your database and branch, and click the "**Connect**" button. Leave this tab open, as you'll need the host shortly.

2. In the Artie dashboard, create a new pipeline and select "**PostgreSQL**" as the source.

   > [!NOTE]
   >
   > Artie's **PlanetScale** source is for PlanetScale Vitess databases. For PlanetScale Postgres, use the **PostgreSQL** source.

3. Fill in the connection information. TLS is enforced automatically, so no additional TLS configuration is required.

   * **Host**: Paste in the host from the PlanetScale **Connect** page, for example `xxxxxxxxxx-useast1-1.horizon.psdb.cloud`. If you use PrivateLink, use the Private Host instead.
   * **Port**: `5432`
   * **User**: `artie_transfer.<branch_id>`
   * **Password**: The password you set for `artie_transfer`
   * **Database name**: Select the database your tables are in (`postgres` by default)

   > [!WARNING]
   >
   > Connect directly to the primary on port `5432`. Don't use the PgBouncer port (`6432`) or add `|replica` to the username.

4. For **Publication**, select `dbz_publication` from the list. Artie sets **Publication mode** and **Publish via partition root** to match. If you set them by hand, they must match the publication, or deployment fails validation, for example with `please set the publication mode to 'all tables' to match the existing publication`.

5. Under **Advanced options**:

   * Turn on **Failover slot**, so Artie creates its replication slot with `failover = true`. PlanetScale requires this for the slot to survive a switchover or failover. This setting doesn't change a slot that already exists. See [Enable failover on an existing slot](#enable-failover-on-an-existing-slot).
   * If you created a heartbeat table, turn on **Heartbeats**. Artie shows SQL for the heartbeat table, which you already ran, so you can skip it.

6. Select the tables you want to replicate. For a table without a primary key, set **Override primary keys** in its table settings and set the table to `REPLICA IDENTITY FULL`. Otherwise, deployment fails with `the following tables are missing primary keys`.

7. Select your destination, review the pipeline settings, and click "**Deploy**."

## Sync overview

Artie starts the initial copy and change capture together:

1. **Backfill**: Artie reads the selected tables and loads their existing rows into your destination.
2. **Streaming**: Artie reads inserts, updates, and deletes from its replication slot while the backfill is running and afterward.

Changes captured during the backfill are queued.
When a table's backfill finishes, Artie applies those changes in order so the destination does not retain stale backfill values.
Artie creates destination tables automatically and adds new columns as your schema changes.

## Manage your pipeline

### Enable failover on an existing slot

> [!WARNING]
>
> If PlanetScale detects a logical replication slot with `failover = false`, you will be notified and given 3 days to update the configuration.
> If the slot is not reconfigured within that window, PlanetScale will resume performing planned failovers as normal, and Artie's slot won't survive the next switchover or failover.

A slot that Artie created before **Failover slot** was turned on has `failover = false`.
Using the default role, check Artie's slot:

```sql theme={null}
SELECT slot_name, active, failover FROM pg_replication_slots WHERE slot_type = 'logical';
```

If the `failover` column shows `f`:

1. In Artie, pause the pipeline with **Pause reading & delivery** so it releases the slot. The default, **Pause delivery only**, stays connected to the slot.
2. Follow [Enabling failover on an existing replication slot](https://planetscale.com/docs/postgres/integrations/logical-cdc.md#enabling-failover-on-an-existing-replication-slot).
3. Resume the pipeline. Artie continues from its last acknowledged position.

### Pausing a pipeline

While a pipeline is paused with **Pause reading & delivery**, its slot holds back WAL.
Keep pauses short:

* If Artie falls further behind than `max_slot_wal_keep_size`, Postgres invalidates the slot. Artie can no longer resume from it, and the pipeline needs a new slot and a backfill.
* A failover can proceed before an idle slot is synced to the new primary, which risks losing the slot. `logical_slot_sync_timeout` bounds how long a failover waits for slot sync. For more details, see [Postgres High Availability with CDC](https://planetscale.com/blog/postgres-ha-with-cdc).

Monitor replication lag and WAL retention with PlanetScale's [CDC monitoring queries and metrics](https://planetscale.com/docs/postgres/integrations/logical-cdc.md#monitoring-and-troubleshooting).

### Removing a pipeline

Artie doesn't drop its replication slot or your publication when you delete a pipeline.
After the pipeline has disconnected, check whether its slot still exists and drop it so it stops holding WAL.
As the default role, run:

```sql theme={null}
SELECT pg_drop_replication_slot('artie');
DROP PUBLICATION dbz_publication;
```

Replace `artie` and `dbz_publication` with your own names if you changed them in Artie.
Postgres can't drop a slot while it's active.
Only drop the publication if no other pipeline uses it.

### Branches

Artie's credentials target one branch, so each branch needs its own pipeline.
A new, empty branch doesn't include the parameters, user, heartbeat table (if you use one), or publication from these steps, so repeat them on that branch and create a separate Artie pipeline with its credentials.
The branch ID in the username changes too.

A branch restored from a backup includes the backup's data, so `artie_transfer`, the heartbeat table, and the publication may already exist there.
Check them before you repeat these steps.

## Need help?

For help with Artie, contact Artie support or see the [Artie documentation](https://www.artie.com/docs/quickstart).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
