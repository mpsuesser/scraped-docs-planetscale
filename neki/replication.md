---
url: https://planetscale.com/docs/neki/replication
title: "Replication"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Replicator workflows

> Copy and change streaming for migrations and online schema changes

The Neki Replicator builds a copy of live table data on a new target while applications continue using the source shards.
It copies the existing rows first, then streams new changes until the target catches up.

Neki uses this process to move tables, redistribute rows across shards, and rebuild tables for online schema changes.
It lasts only for the workflow and is separate from Postgres physical replication, which supports replicas used for reads and failover.

## Workflows

Three operations use Replicator workflows:

* **MoveTables** moves selected tables to another database, or to a shard
  group whose physical shards are not the source shards.
* **Reshard** redistributes declared tables in one source shard group into a
  target shard group in the same database. After an [unsharded
  import](imports/postgres.md), Reshard is the path that shards those
  tables.
* **Online schema changes** can [build a shadow table and keep it
  current](schema-changes.md#one-workflow-across-all-shards) until the shadow is
  ready.

Not every schema change requires data movement.
Changes that Neki can apply directly do not run a copy-and-stream workflow.

Each workflow creates one or more independent Replicator streams. A stream
connects one source shard to one target shard. When parallel table streams are
enabled, several streams can run between the same pair. A workflow that spans
several source or target shards therefore makes progress through several
streams rather than through one deployment-wide stream.

During a Reshard of a large `events` table, each stream reads from one source shard and writes
to one target shard.
The [data topology](data-topology.md) determines which copied rows and later source changes belong to that target.

## Copy and stream

The copying stage transfers rows that already exist. Neki copies tables in
batches so that a large table does not need to be moved in one transaction.
After each copy cycle, Neki applies WAL changes for the rows already copied,
reducing WAL retention. Within one stream, tables are copied one after another.
A workflow can still copy concurrently because it can have several streams.

Each copy cycle reads from a consistent source snapshot while the source remains
available for reads and writes. Changes made during the copy are also sent to
the target, so it can catch up without requiring the application to stop writing.

Copying and streaming add work to both the source and target.
The impact is greater for large tables and write-heavy workloads.

After the initial copy finishes, Neki streams new source changes to the target
until the workflow completes or switches traffic.

## Progress and resumption

Neki records progress as data is copied and streamed. If a stream is interrupted,
it resumes from committed progress instead of restarting the entire copy.

Reaching the streaming stage does not switch application traffic by itself.
Before routing changes or a shadow table takes over, Neki waits for the
target to catch up.

Until the target takes over, applications continue reading from and writing to
the source. Replicator workflows do not merge changes written independently
to the target.

[Data migration](data-migration.md#moving-traffic) explains the traffic
switch, cutover checks, and completion stages for online DDL.

## What Replicator does not cover

Replicator workflows are only used for temporary data-movement workflows.
Other Neki features use different systems:

* **Postgres replicas** use physical replication for availability and
  failover.
* **[Reference-table
  writes](reference-tables-and-gsis.md#reference-table-writes)**
  update every copy as part of the original write.
* **[Global secondary
  indexes](reference-tables-and-gsis.md#gsi-mappings-for-writes)** are
  updated with the original table write.
* **Query fanout** sends a query to multiple existing shards without creating
  another copy of the data.
* **Direct schema changes** are [applied without a copy-and-stream
  workflow](schema-changes.md#how-direct-ddl-works).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
