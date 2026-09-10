---
url: https://planetscale.com/docs/neki/schema-changes
title: "Schema Changes"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

A statement that changes a large or busy table can hold locks, rewrite existing rows, build an index, generate large volumes of write-ahead log (WAL), and consume substantial database resources. These effects can increase query latency or stop application traffic.

Sharding adds another requirement: every shard must end with the same table definition. Applying a change independently on each shard makes progress and failure difficult to track. It can also leave some shards on the old definition and others on the new definition.

Neki supports both native DDL and managed DDL. Native DDL means issuing a supported statement such as `ALTER TABLE` through a router. Neki sends the statement to every managed shard, where Postgres executes it immediately. Native DDL has no workflow record, progress tracking, readiness gate, or explicit completion step.

After native DDL commits, the change is committed across the managed shards, but another router may not have refreshed its local schema view yet. The router that accepts the DDL emits a notice containing the exact `__neki.wait_for_ddl(schema_version, cluster_version)` call for that change. Run that call before sending dependent SQL through other router instances:

```sql
SELECT __neki.wait_for_ddl(<schema_version>, <cluster_version>);
```

The wait completes when the schema change is visible on every router. A notice or warning about router visibility does not mean that the DDL transaction failed; read the notice and use the supplied wait call to establish the cluster-wide visibility barrier.

During Platform Preview, publication and subscription DDL applies to the managed shards that exist when the statement runs. Neki does not restore those objects when a shard is added or rebuilt. Contact PlanetScale Support before combining Postgres logical replication with shard lifecycle changes.

Managed DDL uses a workflow. The workflow records the requested DDL, tracks progress on every managed shard, waits until the change is ready everywhere, and gives you explicit control over completion and cleanup.

## Online and direct schema changes

Neki’s managed DDL supports two execution paths.

**Online DDL** is designed for changes that would be disruptive when applied to the live table. Examples include changes that rewrite many rows and index creation on a large table. Neki builds an updated shadow table in the background while the application continues to use the original table.

**Direct DDL** sends the change directly to Postgres in a transaction. This path is appropriate for statements that do not need a table copy, and it is the only managed path for statements that the shadow-table process cannot carry. Examples include creating or dropping a table and changes to types, sequences, and views.

Neki examines the requested DDL and selects the appropriate path. You can also choose direct execution for an online-compatible change when applying the change directly is preferable. A request that combines incompatible online and direct work may need to be split into separate workflows.

Both paths use the same managed workflow. Direct DDL receives the same progress tracking, readiness gate, explicit completion, retry handling, and multi-shard coordination as Online DDL.

## How Online DDL works

Online DDL keeps the live table available while Neki builds its replacement:

1. It creates a shadow table beside the live table.
2. It applies the requested DDL to the shadow table.
3. It copies the existing rows from the live table to the shadow table in batches, using a sequence of transactions instead of one transaction for the entire copy.
4. Meanwhile, it streams ongoing inserts, updates, and deletes to the shadow table.
5. It keeps applying changes until the shadow table is caught up and ready for cutover.

The application continues to read and write the original table during these steps. Copying rows and catching up can take a long time, but the batched work limits the size and duration of each transaction.

Cutover is the short final step. Neki first asks routers to buffer new queries for the affected table. Neki then locks the table, applies the final streamed changes, and swaps the original and shadow tables in one Postgres transaction. When the transaction commits, the new table definition is live under the original table name. Buffered queries then continue against the new table.

If Neki cannot acquire the lock within the cutover timeout, it backs off and retries. Application traffic continues between attempts.

## How direct DDL works

Direct DDL does not create a shadow table or copy rows. The shard records that it is ready, then waits for you to complete the workflow. At completion, Postgres applies the DDL in a transaction.

Direct execution can require locks on the live table. The safety of this path therefore depends on the requested operation and the table workload. Neki uses it for statements that are best handled directly, and you can select it deliberately for changes that are safe to run in place.

## One workflow across all shards

A Neki database has one logical table definition across its managed shards. Some shards store rows for a table. Other shards keep the table definition but do not store rows for that table.

For most online-compatible table changes:

- Data-bearing shards copy and catch up a shadow table with Online DDL.
- Schema-only shards wait to apply the same change directly.

For a direct schema change, every managed shard waits on the direct path.

The workflow reports each shard’s progress and does not become ready to complete until every shard is ready:

- An online shard is ready when its shadow table is copied and caught up.
- A direct shard is ready when it is waiting to apply its transaction.

When every shard is ready, complete the workflow once. Neki tells each shard to cut over or apply its direct DDL.

![A schema-change workflow prepares and catches up shadow tables on data-bearing shards while schema-only shards wait for direct DDL. After every shard reports ready, each shard commits independently. Online shards atomically swap tables and schema-only shards apply direct DDL before the Postgres schema converges across managed shards.](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/schema-changes/online-change.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=a06886a462f14a56a8277361cacc86b0)

A schema-change workflow prepares and catches up shadow tables on data-bearing shards while schema-only shards wait for direct DDL. After every shard reports ready, each shard commits independently. Online shards atomically swap tables and schema-only shards apply direct DDL before the Postgres schema converges across managed shards.

![A schema-change workflow prepares and catches up shadow tables on data-bearing shards while schema-only shards wait for direct DDL. After every shard reports ready, each shard commits independently. Online shards atomically swap tables and schema-only shards apply direct DDL before the Postgres schema converges across managed shards.](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/schema-changes/online-change-darkmode.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=4ecd09ccbab7ce425d033da45485fe56)

A schema-change workflow prepares and catches up shadow tables on data-bearing shards while schema-only shards wait for direct DDL. After every shard reports ready, each shard commits independently. Online shards atomically swap tables and schema-only shards apply direct DDL before the Postgres schema converges across managed shards.

Each shard commits its own Postgres transaction. Completion is coordinated, but it is not one atomic transaction across the whole Neki database. In normal operation, shards finish within seconds of one another, although failures can extend that interval.

If one shard completes and another shard fails, Neki preserves the workflow and its per-shard status. The safe recovery direction is forward: clean up the failed attempt, reissue the workflow, then complete the remaining shards so every shard reaches the same table definition.

## Managing the workflow

A schema-change workflow has a small lifecycle.

### Create and track

Creating the workflow records the DDL and starts the requested work on every managed shard. Status reports show overall progress and per-shard progress.

Create a workflow from any Postgres client connected through a Neki router. Double the single quotes inside the DDL string:

```sql
SELECT *
FROM __neki.online_ddl_create(
  'add-refund-state',
  'orders_db',
  'ALTER TABLE public.orders ADD COLUMN refund_state text NOT NULL DEFAULT ''none''',
  ''
);
```

The arguments are the workflow name, database name, DDL, and migration ID. An empty migration ID tells Neki to generate one. The function returns the workflow name and migration ID.

Check the aggregate and per-shard status:

```sql
SELECT workflow, ddl, status, jsonb_pretty(status_by_shard)
FROM __neki.online_ddl_status('add-refund-state');
```

Wait until every shard reports `ddl_status: "running"`, `current_readiness: true`, and `controller_state: "running"`. A `postgres_error` means Neki could not read that shard’s state.

Reissuing Create for an interrupted workflow can restore missing controllers and resume work from persisted state. Depending on where the interruption occurred, a shard may resume or restart its copy and streaming work. A failed attempt must be cleaned up before it can restart.

### Complete

Completion is a separate, explicit action. Neki first verifies that every shard is ready and that no shard has failed. Online shards then cut over, and direct shards apply their DDL.

```sql
SELECT __neki.workflow_complete('add-refund-state');

SELECT workflow, status, jsonb_pretty(status_by_shard)
FROM __neki.online_ddl_status('add-refund-state');
```

Repeated completion requests are safe. A shard that has already completed does not apply the change again. An unfinished shard continues toward the requested end state.

### Cancel

Cancel abandons the workflow before completion. In the normal path, Neki marks each shard cancelled, removes its Online DDL artifacts, and deletes the workflow record only after every shard reaches a safe final state.

```sql
SELECT __neki.workflow_cancel('add-refund-state');
```

Cancellation is rejected while a shard is actively cutting over because its final table state is not yet known. Wait for cutover to settle, inspect `online_ddl_status`, and retry Cancel.

If some shards completed while others cancelled, Neki retains the workflow record instead of hiding the schema skew. Reissue Create with the stored configuration, allow the remaining shards to become ready, and Complete the workflow so every shard converges on the new definition. Do not run blind artifact cleanup across a mixed final state.

### Clean up

Cleanup is a separate action. After a successful complete, it drops the retained original table and the remaining Online DDL artifacts, then removes the workflow record when every shard has completed.

```sql
SELECT __neki.online_ddl_cleanup('add-refund-state');
```

After a failed shard, cleanup removes that shard’s artifacts so you can reissue Create. Neki keeps the workflow in that case. Normal cancellation performs its own cleanup; do not follow a successful Cancel with a separate Cleanup.

If cancellation or cleanup is interrupted, reissuing the operation is safe. Neki uses persisted workflow and per-shard state to resume, retry, or reject an action that would conflict with the recorded outcome.

### Retry a failed shard

Clean up the failed attempt, then reissue Create with the stored workflow configuration:

```sql
SELECT __neki.online_ddl_cleanup('add-refund-state');

SELECT *
FROM __neki.online_ddl_create(
  'add-refund-state',
  '',
  '',
  ''
);
```

Completed shards do not apply the change again. After all shards become ready, run `workflow_complete` and `online_ddl_cleanup` as above.

## Choosing a path

Use native DDL when the change is safe to execute immediately and does not need workflow tracking or an explicit completion gate.

Use managed Online DDL when direct execution could block traffic, rewrite a large live table, or build an index aggressively on the serving table.

Use managed direct DDL when the statement cannot use the shadow-table process, when no copy is needed, or when you want workflow coordination for a change that is safe to run in place.

Common single-table `ALTER TABLE` changes can use the online path, including adding or dropping columns, changing a column type, adding or dropping constraints, and adding an index. `CREATE INDEX` and `DROP INDEX` also use the online path. Do not include `CONCURRENTLY` when creating an index through Online DDL.

Statements that do not need a shadow-table copy use the managed direct path. These include `CREATE TABLE`, `DROP TABLE`, and operations on types, sequences, and views. A table change can also be forced onto the direct path by passing `'{"applyDirect": true}'` as the fifth argument to `online_ddl_create`.

Each workflow must resolve to one affected table and one execution category. Split changes across workflows when they affect multiple tables or combine online and direct work. Changing a table’s shard key or primary routing index is a data-movement operation; use a resharding workflow instead.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
