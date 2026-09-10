---
url: https://planetscale.com/docs/neki/data-migration
title: "Data Migration"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Data migrations move tables to a different placement or reshard them without taking the application offline. Neki copies the existing rows and keeps the target updated as new writes come in. The source continues serving traffic until traffic switches to the target.

If the data starts outside Neki, first [import it into an unsharded Neki database](imports/postgres.md). After that placement is stable, **Reshard** redistributes declared tables in one source shard group onto a new shard group in the same database. **MoveTables** is a different workflow. It copies selected tables to another database, or to a shard group whose physical shards are not the source shards.

During Platform Preview, MoveTables and Reshard are not managed in the PlanetScale dashboard and have no separate CLI workflow. Create, inspect, and operate them with Neki metafunctions on a SQL connection to a Neki router. Mutation functions require `neki_operator`; status and report functions require `neki_viewer`. The default `postgres` role is broader than this workflow authorization contract. Connect to a router that serves primary traffic.

## How workflows are managed

The router is the management entry point for a Neki data-movement workflow. Create, status, start, stop, differ, traffic switch, cancel, and complete are Neki metafunctions. Use `reshard_create` for Reshard and `move_tables_create` for MoveTables. After create, the shared `workflow_*` metafunctions operate either workflow.

There is no dashboard or CLI fallback during Platform Preview. A workflow created through the router continues running after the SQL session that created it disconnects. Later sessions use the workflow name to inspect it or request its next operation.

The sections below explain the lifecycle and the checks required before each operation. They describe Neki workflows for data that is already inside Neki.

### Router metafunctions

| Operation | Metafunction |
| --- | --- |
| Create a Reshard workflow | `__neki.reshard_create(workflow, database, source_shard_group_uid, target_shard_group, target_shard_group_uid, options)` |
| Create a MoveTables workflow | `__neki.move_tables_create(workflow, source_db, source_database_topology, target_db, target_database_topology, source_tables, target_tables, options)` |
| List workflows | `__neki.list_workflows()` |
| Status | `__neki.workflow_status(workflow)` |
| Start or resume | `__neki.workflow_start(workflow)` |
| Stop | `__neki.workflow_stop(workflow)` |
| Create and start a differ | `__neki.differ_create(workflow, differ_name, options)` |
| Differ status | `__neki.differ_status(workflow, differ_name)` |
| Differ report | `__neki.differ_report(workflow, differ_name)` |
| Delete a differ | `__neki.differ_delete(workflow, differ_name)` |
| Switch reads | `__neki.workflow_switch_reads(workflow)` |
| Switch writes | `__neki.workflow_switch_writes(workflow, options)` |
| Switch reads and writes | `__neki.workflow_switch_traffic(workflow, options)` |
| Cancel before traffic switch | `__neki.workflow_cancel(workflow, options)` |
| Complete after cutover | `__neki.workflow_complete(workflow, options)` |

Workflows start when created unless the create options include `"create_stopped": true`. Review a stopped workflow, then call `workflow_start`.

### Workflow options

Create options are a JSON object. Unknown values are rejected. The most useful correctness and load controls are:

| Option | Behavior and constraints |
| --- | --- |
| `create_stopped` | Create the schema, streams, topology merge, and target block without starting data movement |
| `stop_after_copy` | Stop after the initial copy; unlike `create_stopped`, copying begins |
| `copy_batch_size` | Rows per copy batch; `0` uses the format-specific default |
| `copy_phase_duration` | Maximum snapshot-hold duration per copy cycle; `0` uses the default |
| `max_concurrent_table_streams` | Requested streams per source/target shard pair; connection limits can queue excess streams |
| `read_from_standby` | Read the snapshot and bulk-copy data from a source standby; CDC stays on the primary. Requires Postgres 16 or later on the source |
| `on_ddl` | Exact values are `ON_DDL_ACTION_STOP` and `ON_DDL_ACTION_IGNORE`; omitted means `ON_DDL_ACTION_IGNORE` |
| `skip_defer_secondary_keys` | Keep target secondary indexes in place during copy instead of rebuilding them afterward |
| `skip_grants` | Do not replay source grants; apply and verify target privileges separately |
| `skip_source_extensions` | Omit source extension creation from schema deployment; defaults to `true` for an external source and `false` for a Neki-managed source |
| `dry_run` | Validate and report a MoveTables plan without creating it |
| `publication_name` | Reuse a MoveTables source publication; MoveTables only and incompatible with `ON_DDL_ACTION_STOP` |
| `import_cluster_auth` | Import source roles for MoveTables; choose `IMPORT_CLUSTER_AUTH_FULL` or `IMPORT_CLUSTER_AUTH_WITHOUT_PASSWORDS` only after reviewing their target-role effects |
| `skip_existing_roles` | Leave same-named target roles unchanged; requires `import_cluster_auth` |

`dry_run`, `publication_name`, `import_cluster_auth`, and `skip_existing_roles` are MoveTables-only. `ON_DDL_ACTION_STOP` requires PostgreSQL 14 or later and a superuser source login because Neki must create an event trigger. Many managed external sources do not grant that privilege.

Only `copy_batch_size` and `copy_phase_duration` can be changed on a running workflow:

```sql
SELECT * FROM __neki.workflow_set_options(
  'reshard_events',
  '{"copy_batch_size": 500, "copy_phase_duration": "60s"}'
);
```

List the metafunctions your router currently exposes:

```sql
SELECT name, arguments, purpose
FROM __neki.list_metafuncs()
WHERE group_name IN ('workflows', 'cutover', 'verification');
```

![A migration starts, initializes, copies existing rows, streams new changes, switches traffic, and completes. The source serves traffic until the switch, then the target serves. Stop and Start preserve progress. Cancel is available before the switch.](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/data-migration/migration-lifecycle.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=65f158b1f1d598edb71bb12e222fa8ce)

A migration starts, initializes, copies existing rows, streams new changes, switches traffic, and completes. The source serves traffic until the switch, then the target serves. Stop and Start preserve progress. Cancel is available before the switch.

![A migration starts, initializes, copies existing rows, streams new changes, switches traffic, and completes. The source serves traffic until the switch, then the target serves. Stop and Start preserve progress. Cancel is available before the switch.](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/data-migration/migration-lifecycle-darkmode.png?w=2500&fit=max&auto=format&n=6i1lJ6cNfB-VSsaH&q=85&s=b8f627fca7b0a8595f4bb8c0c5c643de)

A migration starts, initializes, copies existing rows, streams new changes, switches traffic, and completes. The source serves traffic until the switch, then the target serves. Stop and Start preserve progress. Cancel is available before the switch.

## What a migration workflow moves

Neki has two workflows for changing where existing data lives:

| Workflow | What changes |
| --- | --- |
| **MoveTables** | Selected tables move to another database, or to a shard group whose physical shards are not the source shards |
| **Reshard** | Declared base tables in one source shard group are redistributed into a target shard group within the same database |

Workflows start by default. They can instead be created in a stopped state, reviewed, and started later. Throughout the move, the [data topology](data-topology.md) determines which target shard receives each row.

Before creating a Reshard workflow, declare every physical base table that resolves to the source group in the data topology. A declared table can inherit its shard group from a database, schema, or cluster default. Neki refuses to start if a physical table uses that default but has no table entry in the data topology.

At cutover, database and schema defaults that point to the source group move to the target group. If the database used the cluster default, Neki adds an explicit database default for the target group.

A Reshard workflow can split the initial authoritative shard group. When its target contains multiple shards, table placement moves to the target group but cluster authority remains on the original single-shard group. The original group continues to provide database authority and hold system tables and sequences that cannot move to the multi-shard target. The original shard does not become one of the data shards in the split. A two-way split of existing data therefore uses three shards.

## Reshard existing tables

The example redistributes `public.events` by `tenant_id` from one unsharded source shard onto two new shards. Replace shard UIDs, the database name, and the table list with values from your branch.

You do not need to shard every table. Bind only the tables that must move, and the tables that must stay colocated with them, to the source group. Leave tables that should stay unsharded on the [authoritative shard group](data-topology.md#authoritative-shard-group).

Declare every physical base table that resolves to the source group, including tables that inherit that group from a database, schema, or cluster default. Put related foreign-key tables in the same group. Reshard does not automatically repoint dependent views.

### Add destination shards

Add two new shards for a two-way split. Wait until each destination shard has a ready primary. Do not reuse the source shard as a target.

#### Dashboard

Open the database, go to **Clusters**, select the configuration profile, open the **Shards** tab, and select **Create new shards**. Enter `2`, review the estimated cost, and add the shards.

#### CLI

```shellscript
pscale branch shard create <DATABASE_NAME> main \
  --config-profile default \
  --count 2
```

```sql
SELECT uid, name FROM __neki.list_shards();
```

### Declare the source group

Creating shards does not move existing rows. Apply a topology that keeps serving the current shard and binds the tables you will reshard to a named source group on that same shard. Declare the shard index the target group will use. A topology update is a complete replacement. You can edit the document in **Clusters** > **Data topology**, or apply it with SQL; see [Data topology](data-topology.md).

```sql
SELECT * FROM __neki.set_data_topology(
  $$
  {
    "shard_indexes": {
      "xxhash_tenant_id": {
        "type": "xxhash",
        "columns": ["tenant_id"]
      }
    },
    "shard_groups": [
      {
        "uid": "<SOURCE_SHARD>",
        "key_ranges": [
          { "shard_uid": "<SOURCE_SHARD>" }
        ]
      },
      {
        "uid": "imported",
        "key_ranges": [
          { "shard_uid": "<SOURCE_SHARD>" }
        ]
      }
    ],
    "databases": {
      "postgres": {
        "schemas": {
          "public": {
            "tables": {
              "events": { "shard_group": "imported" }
            }
          }
        }
      }
    },
    "default_shard_group": "<SOURCE_SHARD>",
    "authoritative_shard_group": "<SOURCE_SHARD>"
  }
  $$::text,
  true
);
```

```sql
SELECT __neki.wait_for_data_topology(<revision>);
```

### Create, copy, switch, and complete

Workflows start when created unless you pass `"create_stopped": true`. The target group’s `shard_uid` values must be the two new shards only.

```sql
SELECT __neki.reshard_create(
  'reshard_events',
  'postgres',
  'imported',
  '{
    "default_shard_index": "xxhash_tenant_id",
    "key_ranges": [
      {"shard_uid": "<SHARD_2>", "end": "80"},
      {"shard_uid": "<SHARD_3>", "start": "80"}
    ]
  }',
  'events_by_tenant',
  '{"create_stopped": true}'
);

SELECT __neki.workflow_start('reshard_events');
SELECT * FROM __neki.workflow_status('reshard_events');
```

Wait until every expected stream is `running` in the `streaming` phase. Then create a differ, review the report, and switch traffic. Confirm streams are streaming again after the differ; cutover refuses streams that are not.

```sql
SELECT __neki.differ_create('reshard_events', 'pre_cutover');
SELECT * FROM __neki.differ_status('reshard_events', 'pre_cutover');
SELECT * FROM __neki.differ_report('reshard_events', 'pre_cutover');

SELECT * FROM __neki.workflow_switch_traffic('reshard_events');
SELECT __neki.workflow_complete('reshard_events');
```

The later sections cover differ acceptance, staged read/write switches, cancel, and source cleanup.

## From copy to streaming

A migration first copies rows that already exist. Neki works in batches while continuing to incorporate changes committed on the source. Once the initial copy is finished, the workflow enters `streaming` and continues applying new source changes to the target.

Reaching `streaming` means the target is being kept current. Reads and writes still go to the source until traffic switches to the target.

### Copying with an iteration key

Each table needs an **iteration key**: a stable, unique order in which Neki can copy its rows. Neki prefers the primary key. If a table has no primary key, a suitable non-nullable unique key can serve instead.

The iteration key controls copy order and resumption. The shard key determines where a row belongs.

`public.events` is sharded by `tenant_id` and has a primary key named `event_id`. Neki can copy the table in `event_id` order while using `tenant_id` to choose the target shard. When a batch commits, its rows and copy progress are saved together. Later batches continue from that committed point while source changes affecting earlier rows are incorporated into the target.

A migration stream does not apply source data definition language (DDL) changes to the target. Set `on_ddl` to the exact value `ON_DDL_ACTION_STOP` to watch migrated source tables and stop for relevant table-shape changes, schema renames, and unrecognized changes associated with a migrated table. The stop occurs before row changes from that source transaction are applied. This stop is terminal. Continuing requires cancelling and recreating the workflow, which copies the data again under the new schema.

`ON_DDL_ACTION_STOP` requires PostgreSQL 14 or later and a superuser source login because Neki must create an event trigger. Many managed external sources do not grant that privilege, so this mode is unavailable for those imports. It also cannot be combined with `publication_name`.

If `on_ddl` is omitted, Neki treats it as `ON_DDL_ACTION_IGNORE`. Set that exact value to select the behavior explicitly. The stream keeps running, so you must keep the source and target schemas compatible.

Index and constraint changes do not trigger that stop, even when they alter replica identity indirectly. Replica identity determines how PostgreSQL finds rows for updates and deletes. Continued streaming does not prove that the schemas remain compatible. The migration stream does not apply those schema changes to the target.

Because the migration stream does not apply DDL, source and target schemas must be kept compatible through separate schema changes.

See [Schema changes](schema-changes.md) for Online DDL and direct schema-change workflows.

### Durable progress and resumption

Neki saves copy progress with the rows in each committed batch. It also saves streaming progress with the changes applied on the target. If the workflow is interrupted, it resumes from durable progress rather than recopying the entire table.

## Reading migration progress

Inspect one workflow or list every workflow:

```sql
SELECT * FROM __neki.workflow_status('reshard_events');

SELECT workflow, type, status, traffic_state, phase,
       streams_streaming, streams_copying, streams_error
FROM __neki.list_workflows();
```

An active migration stream moves through these phases:

| Phase | Meaning |
| --- | --- |
| `initializing` | Neki is validating and preparing the migration |
| `copying` | Existing rows are still being transferred |
| `streaming` | Initial copy is complete and source changes continue reaching the target |

A stream also reports whether it is running, stopped, or in error. The traffic state shows whether the source or target serves application traffic.

Status is reported for each stream and target shard. Before traffic can move, every expected stream must be running and in the `streaming` phase. Copy progress ends at `streaming`. Cutover and completion are separate, explicitly requested stages.

### Stop and Start preserve progress

Stop pauses a workflow without abandoning the migration. Its saved phase and progress remain in place. Start resumes the workflow from that state. A stopped workflow stays stopped until it is started again.

Use Stop when the migration should continue later. Cancel removes the workflow. By default, Cancel also cleans up data created or copied on the target. You can choose to keep that data.

```sql
SELECT __neki.workflow_stop('reshard_events');
SELECT __neki.workflow_start('reshard_events');
```

## Validate before cutover

A **differ** compares the source and target rows for a workflow. Creating a differ starts it. Its report shows mismatched rows and rows found only on the source or target.

```sql
SELECT __neki.differ_create('reshard_events', 'pre_cutover');
SELECT * FROM __neki.differ_status('reshard_events', 'pre_cutover');
SELECT * FROM __neki.differ_report('reshard_events', 'pre_cutover');
```

Review the differ report before switching traffic. Cutover readiness checks that every expected stream is running and streaming. It does not check the differ.

Approve cutover only when all of the following are true:

- The top-level report has `complete` set to `true`.
- `mismatch` is `false`.
- Shard errors are empty or absent.
- Every expected table is present in `table_results`.
- Every table has `completed` set to `true`.
- The report finds no missing, extra, or mismatched rows.

Counts are partial when `complete` is false or a table’s `completed` field is false. `rows_compared` of `0` does not prove that an unfinished table is empty.

Do not treat a complete differ without mismatches as the only proof that a migration is ready. Confirm that every expected table and stream was included, review the compared and unmatched row counts, and perform an independent application-level check of critical tables before cutover.

After the differ finishes, confirm every stream is `running` in `streaming` again before switching traffic. The differ can restart streams, and cutover refuses streams that are not streaming.

## Moving traffic

Traffic can move all at once or in stages. A staged switch moves non-primary reads (`replica` and `rdonly`) first while primary reads and writes stay on the source. Writes move in a separate step after the target has served read traffic.

```sql
SELECT * FROM __neki.workflow_switch_reads('reshard_events');
SELECT * FROM __neki.workflow_switch_writes('reshard_events');
```

To move reads and writes together:

```sql
SELECT * FROM __neki.workflow_switch_traffic('reshard_events');
```

During a write switch, Neki buffers affected queries, prevents new source writes to the tables being moved, and waits for previously accepted changes to reach the target. It then updates the routing topology so reads and writes use the target.

Before updating the topology, Neki synchronizes sequences owned by the tables being moved. It finds the highest value in each owning column across the target shards and sets the sequence on its resolved shard to that value. This prevents the next sequence value from colliding with a copied row.

The switch can be retried if it is interrupted. Once the cutover decision is durable, retries finish the switch instead of returning serving authority to the source.

Returning traffic to the source after writes have moved is not a documented Platform Preview rollback path. Do not plan cutover around reversing a write switch.

## Cancel or Complete

Choose the operation from the current traffic state and the intended outcome:

| Situation | Operation | Result |
| --- | --- | --- |
| Traffic has not moved and the migration should be abandoned | **Cancel** | Stop and remove the workflow. Disposable target data is removed by default unless it is explicitly retained |
| Traffic has moved and the target is final | **Complete** | Retire migration resources and close the workflow |

```sql
SELECT __neki.workflow_cancel('reshard_events');
```

You cannot use Cancel after traffic has moved. Unlike Stop, Cancel cannot be resumed. Pass `{"keep_data": true}` to retain target data.

Complete is separate from the traffic switch. It removes the workflow’s temporary migration resources after the target has become final. Source data is retained by default for safety.

```sql
SELECT __neki.workflow_complete('reshard_events');
```

To truncate old source rows after you no longer need them:

```sql
SELECT __neki.workflow_complete(
  'reshard_events',
  '{"drop_source_data": true}'
);
```

After you verify the move, you can choose how to handle the old source data. For MoveTables, you can rename an old source table to `_<table>_old`, so `events` becomes `_events_old`. If you choose source cleanup, Neki truncates tables that keep the same database, schema, and name. It drops old source tables that moved to a different database, schema, or name. You cannot both rename and remove the old source data.

Reshard keeps table identifiers unchanged, so its source tables cannot be renamed. Neki does not drop these tables during Complete. If you choose source cleanup, it truncates their rows on the old source shards. You can remove an old source shard later if it no longer holds other data and it is not the [authoritative shard group](data-topology.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
