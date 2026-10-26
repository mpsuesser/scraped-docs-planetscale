---
url: https://planetscale.com/docs/vitess/integrations/artie
title: "Artie"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Artie integration

> Connect PlanetScale Vitess to Artie so you can extract data and safely load it into other destinations for analysis, transformation, and more.

This page covers Vitess.

Artie continuously replicates inserts, updates, and deletes from PlanetScale into supported warehouses, lakehouses, and object stores.
Artie connects through Vitess VStream rather than standard MySQL binary-log replication, so you do not need to configure binlog replication, GTID, or MySQL replication privileges.
In this section, you'll learn how to set up Artie and connect your PlanetScale source.

## Connect to Artie

### Requirements

* A PlanetScale Vitess database
* An Artie account
* A destination configured in Artie. See Supported destinations below.

#### Connect using private networking

If you are using private networking and not connecting directly to your PlanetScale database, the Artie integration supports AWS PrivateLink and SSH tunnels.
See the Artie AWS PrivateLink setup guide or Artie SSH tunnel setup guide for details.

### Set up Artie

1. In PlanetScale, navigate to the database you want to connect to Artie and click the "**Connect**" button.

2. Create a new password for the branch you want to replicate with read-only permissions. The read-only role provides the `SELECT` access Artie needs for the initial backfill and the VStream access it needs for ongoing replication. The password you create determines which branch Artie replicates. To replicate another branch, create a separate Artie source with a password for that branch.

3. Copy the connection values from the password details.

4. In the Artie dashboard, create a new pipeline and select "**PlanetScale**" as the source. Fill in the connection information:

   * **Host**: Paste in the copied value for `host`
   * **Port**: `3306`
   * **Username**: Paste in the copied value for `username`
   * **Password**: Paste in the copied value for `password`
   * **Database**: The keyspace to replicate. This is usually the database name. The password that you create sets the branch.

   TLS is enforced automatically. No additional TLS configuration is required.

5. Allow traffic on both ports Artie uses. The backfill and metadata connection uses port `3306`. Change capture (VStream) uses port `443`. If your password has IP restrictions, or if you use firewall rules, let traffic through on both ports for the Artie fixed IP addresses in the region that runs your pipeline. For other network setups, contact Artie support.

6. Select the tables you want to replicate. Tables are identified by keyspace and table name. Tables from different keyspaces are replicated to separate destination tables by default. See Multiple keyspaces below.

7. Select your destination, review the pipeline settings, and click "**Deploy**." Artie validates read access and the VStream endpoint, begins the initial backfill, and starts capturing changes. **Note:** If validation reports a VStream credential, connectivity, or timeout error, confirm that the password has the read-only role and that Artie can reach your PlanetScale endpoint on ports `3306` and `443`.

### Supported destinations

Artie can replicate Vitess data to the following destinations. See the Artie documentation for destination-specific setup.

* Data warehouses: Snowflake, BigQuery, Databricks, Redshift, ClickHouse, MotherDuck
* Lakehouses: Apache Iceberg (including Amazon S3 Tables and REST Catalog deployments), Delta Lake
* Object storage: Amazon S3, Google Cloud Storage

## Sync overview

Artie starts the initial copy and change capture together:

1. **Backfill**: Artie reads the selected tables and loads their existing rows into your destination.
2. **Streaming**: Artie subscribes to VStream and captures inserts, updates, and deletes that occur while the backfill is running and afterward.

Changes captured during the backfill are queued. When a table's backfill finishes, Artie applies those changes in order so the destination does not retain stale backfill values.

VStream is part of Vitess. Because Artie receives row-level changes through VStream, this integration does not use the MySQL binlog and does not require `REPLICATION CLIENT` or `REPLICATION SLAVE` privileges, binlog retention settings, GTID, or a replica. A read-only password provides all the access Artie needs.

### Multiple keyspaces

Vitess namespaces tables by keyspace. In Artie, you select and browse one keyspace at a time. A table's source identity includes its keyspace, so tables with the same name in different keyspaces are replicated separately by default.

Artie can unify a table across keyspaces so the same logical table lands in one destination table. Enable "**Unify across keyspaces**" in the table's pipeline settings. You can select the keyspaces in two ways:

* **Static**: A list of keyspaces. Artie backfills the selected table in each keyspace and then captures its changes through VStream. To include a new keyspace, add it to the list and run a backfill.
* **Dynamic**: A regular expression, such as `^customer_(us|eu)$`. Artie finds new keyspaces that match and replicates from them. Discovery runs at most every five minutes.

Only unify tables that have compatible schemas and the same merge-key semantics.

## Schema information

Artie creates a destination table for each selected table and maps source column names and types to the closest supported destination types. See Artie's typing library for type-mapping details.

### Artie-generated columns

Artie can add columns prefixed with `__artie_` to destination tables. Optional columns are configured in the pipeline's table or destination settings.

| Column | Type | Description |
| - | - | - |
| `__artie_delete` | `BOOLEAN` | `true` when the row was deleted in your database. Added when soft deletes are enabled. |
| `__artie_operation` | `STRING` | `r` for backfill, `c` for create, `u` for update, and `d` for delete. Optional in normal mode and included in history records in history mode. |
| `__artie_updated_at` | `TIMESTAMP_TZ` | When Artie processed the change. Added in history mode. |
| `__artie_db_updated_at` | `TIMESTAMP_TZ` | When the change occurred in the source database. Added in history mode. |
| `__artie_full_source_table_name` | `STRING` | Fully qualified source table name. Optional. |
| `__artie_source_metadata` | `JSON` | Source-specific event metadata. Optional. |

See System columns for the complete list and destination-specific availability.

### Adding and dropping columns

Artie detects new source columns. For eligible pipelines, a company admin can enable automatic schema reconciliation to add missing source columns to an existing destination table. By default, Artie does not rename or change the type of an existing destination column. Dropping destination columns requires the opt-in hard-delete setting and its safety checks. See schema evolution for supported changes and configuration.

## Excluding source data

Choose the tables to replicate when you create the pipeline, then add or remove tables at any time. Within a table, use column inclusion and exclusion to choose which columns Artie writes to the destination. You can also hash selected columns before Artie writes them.

## Initial sync

When you deploy a pipeline, Artie backfills each selected table, then continues applying VStream changes. You can run a new backfill for a table from the Artie dashboard without recreating the pipeline. See Backfills for behavior and controls.

## Updating data

After the initial sync, Artie applies VStream inserts, updates, and deletes to the destination using the table's merge key. Artie batches changes and flushes them to the destination on a configurable interval. See flush rules to balance freshness against destination load.

### Deleted rows

By default, a row deleted in your database is deleted from the destination.

Enable **soft deletes** to retain the row and set `__artie_delete = true`. Enable **history mode** to create a separate `{TABLE}__HISTORY` table that records every insert, update, and delete with operation and timestamp columns.

## Need help?

For help with Artie, contact Artie support or see the [Artie documentation](https://www.artie.com/docs/quickstart).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
