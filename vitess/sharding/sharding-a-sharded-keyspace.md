---
url: https://planetscale.com/docs/vitess/sharding/sharding-a-sharded-keyspace
title: "Sharding A Sharded Keyspace"
description: ""
access_date: 2026-10-02T19:13:10.531Z
current_date: 2026-10-02T19:13:10.531Z
---

Adding or removing shards from a sharded keyspace requires some rebalancing, as you may be moving data from one MySQL instance to another. In PlanetScale, this is done with a Vitess MoveTables [workflow](../scaling/workflows.md) from one sharded keyspace to another. This involves creating a new sharded (target) keyspace with the new desired number of shards and transferring all of the data from your current (source) keyspace to the new target. This operation is done with no downtime.

The steps in this documentation are similar to those in the [Sharding quickstart](sharding-quickstart.md), however, there is one important addition that you cannot skip. Be sure to review [Step 7: Remove `"require_explicit_routing": true`](sharding-a-sharded-keyspace.md#step-7-remove-require_explicit_routing-true), as it is a crucial step that differs from sharding an unsharded keyspace.

- If you are sharding an existing table in an *unsharded* keyspace, follow the instructions in the [Sharding quickstart documentation](sharding-quickstart.md).
- If you are creating a new table that you want in your existing sharded keyspace, follow the instructions in the [Sharding new tables documentation](sharding-new-tables.md).
- If you simply need to adjust the size of each shard, and not the number of shards, you can do so from the [Clusters](../cluster-configuration.md) page in the dashboard.

These are advanced configuration settings that expose some of the underlying Vitess configuration of your cluster. Misconfiguration can cause availability issues. We recommend thoroughly reading through the documentation in the [Sharding section](../sharding.md) of the docs prior to making any changes. If you have any questions, please [reach out to our support team](https://planetscale.com/contact?initial=support).

Throughout this guide, we will refer to the source keyspace and target keyspace, which are defined as follows:

- **Source keyspace**: The original sharded keyspace from which you are moving the tables you wish to shard.
- **Target keyspace**: The new sharded keyspace that you are moving the selected tables to.

You run the move with [`pscale branch vtctl move-tables`](../../cli/move-tables.md), so make sure you have the [`pscale` CLI installed and authenticated](../../cli/planetscale-environment-setup.md). You must be an [Organization Administrator](../../security/access-control.md#organization-administrator) or a [Database Administrator](../../security/access-control.md#database-administrator) of the database.

This guide also assumes that you either are already using `@primary` in your application code to [target your keyspaces](targeting-correct-keyspace.md) or you do not directly set a database name in your application code.

## Pre-sharding checklist

There is a small amount of upfront work that needs to happen prior to sharding your table(s) again.

### 1\. Prepare to move all table(s) from source keyspace

Some common signals that a table may benefit from further sharding include:

- The table has become very large and query performance has degraded due to this
- Schema changes to the table take hours
- You expect the table to grow quickly and want to shard it further before it becomes a problem

We strongly recommend moving all table(s) from the source keyspace to the target keyspace, so that by the end of this process your database has at most two keyspaces: one unsharded keyspace, and one sharded keyspace.

### 2\. Create another sharded keyspace

To set up another sharded keyspace:

### 3\. Add "require\_explicit\_routing": true

If you are using [Vitess global routing](https://vitess.io/docs/reference/features/global-routing/) (for example, if you are using `@primary`), you will get ambiguous table errors once you add Vindexes to your new keyspace.

To prevent this error, you **must** temporarily add `require_explicit_routing` to your new keyspace’s VSchema:

```text
{
  "sharded": true,
  "require_explicit_routing": true,
}
```

This will instruct Vitess’s global routing to exclude your new keyspace from routing until explicitly targeted. This is just temporary. You will remove this later in this tutorial *before* completing the workflow.

You can choose to do this step, and the following step, by one of the following methods:

- **Safe migrations off**: modify the target keyspace VSchema directly on the Clusters page
- **Safe migrations on**: modify the target keyspace VSchema using deploy requests

### 4\. Copy Vindexes and auto-increment VSchema settings

Assuming your sharding scheme will remain the same, once you’ve completed the previous step of adding `"require_explicit_routing": true`, you can copy the relevant parts of your source keyspace VSchema into your target keyspace VSchema.

Since we recommend moving all tables from the source to the target keyspace, your target VSchema will look exactly the same as your source VSchema, with the addition of `"require_explicit_routing": true`. For example, if you are moving the tables `users` and `exercise_logs`, and your source keyspace VSchema looks like this:

```json
{
  "sharded": true,
  "vindexes": {
    "xxhash": {
      "type": "xxhash"
    }
  },
  "tables": {
    "exercise_logs": {
      "column_vindexes": [
        {
          "name": "xxhash",
          "columns": ["user_id"]
        }
      ],
      "auto_increment": {
        "column": "id",
        "sequence": "\`unsharded\`.\`exercise_logs_seq\`"
      }
    },
    "users": {
      "column_vindexes": [
        {
          "name": "xxhash",
          "columns": ["id"]
        }
      ],
      "auto_increment": {
        "column": "id",
        "sequence": "\`unsharded\`.\`users_seq\`"
      }
    }
  }
}
```

Your target keyspace VSchema will look like this:

```json
{
  "sharded": true,
  "require_explicit_routing": true,
  "vindexes": {
    "xxhash": {
      "type": "xxhash"
    }
  },
  "tables": {
    "exercise_logs": {
      "column_vindexes": [
        {
          "name": "xxhash",
          "columns": ["user_id"]
        }
      ],
      "auto_increment": {
        "column": "id",
        "sequence": "\`unsharded\`.\`exercise_logs_seq\`"
      }
    },
    "users": {
      "column_vindexes": [
        {
          "name": "xxhash",
          "columns": ["id"]
        }
      ],
      "auto_increment": {
        "column": "id",
        "sequence": "\`unsharded\`.\`users_seq\`"
      }
    }
  }
}
```

## Move the tables with MoveTables

The examples below use a database named `mydb`, the `main` production branch, the current `metal-sharded` keyspace as the source, and the new `metal-sharded-2` keyspace as the target.

### Step 1: Create the workflow

```shellscript
pscale branch vtctl move-tables create mydb main \
  --workflow reshard_metal \
  --source-keyspace metal-sharded \
  --target-keyspace metal-sharded-2 \
  --all-tables \
  --defer-secondary-keys \
  --format json
```

This creates a MoveTables workflow named `reshard_metal` that moves every table from the source keyspace, and starts it right away. To move only some tables, pass `--tables` with a comma-separated list instead of `--all-tables`. `--defer-secondary-keys` creates secondary indexes after the copy finishes, which makes the copy much faster.

### Step 2: Watch the copy

As soon as the workflow starts, Vitess copies rows of the tables from your source keyspace to your target keyspace. It uses a combination of `SELECT * FROM table` and binlog-based replication, and redistributes the rows across the target keyspace’s shards using the Vindexes in its VSchema.

Check progress with `status`:

```shellscript
pscale branch vtctl move-tables status mydb main \
  --workflow reshard_metal \
  --target-keyspace metal-sharded-2 \
  --format json
```

Once every table is copied, the streams move to `Running`: Vitess keeps replicating every new write on the source keyspace to the target keyspace, while the source keyspace still serves all primary and replica traffic.

You can also follow the workflow from the dashboard. Click “ **Workflows** ” in the left nav, select your branch, and open the workflow to see its streams, per-table copy progress, replication lag, and traffic routing.

### Step 3: Verify data consistency

Once the streams are `Running`, verify that the source and target keyspaces hold the same data with a VDiff:

```shellscript
pscale branch vtctl vdiff create mydb main \
  --workflow reshard_metal \
  --target-keyspace metal-sharded-2 \
  --format json
```

Pass the `uuid` from the output to `vdiff show`, and repeat until the VDiff completes:

```shellscript
pscale branch vtctl vdiff show mydb main \
  --workflow reshard_metal \
  --target-keyspace metal-sharded-2 \
  --uuid <VDIFF_UUID> \
  --format json
```

If the VDiff reports mismatches, do not switch traffic until you have resolved them.

### Step 4: Switch replica traffic

Switch replica traffic first to test reads from the new keyspace:

```shellscript
pscale branch vtctl move-tables switch-traffic mydb main \
  --workflow reshard_metal \
  --target-keyspace metal-sharded-2 \
  --tablet-types REPLICA,RDONLY \
  --format json
```

You can skip this step and switch primary traffic directly. Pass `--dry-run` to see what a switch would do without applying it.

### Step 5: Switch primary traffic

When reads from replicas look good, switch primary traffic:

```shellscript
pscale branch vtctl move-tables switch-traffic mydb main \
  --workflow reshard_metal \
  --target-keyspace metal-sharded-2 \
  --tablet-types PRIMARY \
  --format json
```

After primary traffic switches, Vitess replicates writes from the target keyspace back to the source keyspace, so both keep the same data until you complete the workflow.

### Step 6: Check traffic in your application

You should now go check out your production application that uses this database to make sure everything is running as expected. You can also check the [Insights](../monitoring/query-insights.md) tab for errors or slow queries.

If you notice an issue, switch traffic back to the source keyspace:

```shellscript
pscale branch vtctl move-tables reverse-traffic mydb main \
  --workflow reshard_metal \
  --target-keyspace metal-sharded-2 \
  --format json
```

### Step 7: Remove "require\_explicit\_routing": true

You should have added `require_explicit_routing` to your target keyspace’s VSchema in step 3 of the “Pre-sharding checklist”:

```text
{
  "require_explicit_routing": true,
  ...
}
```

You’ll need to remove it before completing the workflow.

If you don’t remove this, you may start to see “table not found” errors once the workflow is completed.

### Step 8: Complete the workflow

Up until now, you can cancel the workflow or reverse traffic. Completing the workflow is not reversible: it stops replication between the keyspaces and, with the flags below, drops the moved tables from the source keyspace and removes the routing rules. Preview what `complete` will do with `--dry-run` first:

```shellscript
pscale branch vtctl move-tables complete mydb main \
  --workflow reshard_metal \
  --target-keyspace metal-sharded-2 \
  --keep-data=false \
  --keep-routing-rules=false \
  --dry-run \
  --format json
```

If you have removed `"require_explicit_routing": true` from your target keyspace VSchema, and you are sure you want to proceed, run the same command without `--dry-run`.

- `--keep-data=false` drops the moved tables from the source keyspace. Add `--rename-tables` to rename them instead of dropping them. `--keep-data=true` leaves them in place.
- `--keep-routing-rules=false` removes the routing rules. Pass `--keep-routing-rules=true` to keep them if some queries still name the source keyspace.

Write both flags with an `=`: a space-separated value such as `--keep-data false` is read as `--keep-data=true`.

### Step 9: Check that your production application is working as expected

Finally, check your production application to make sure everything is working as expected. You can check your [Insights](../monitoring/query-insights.md) tab to see if queries are being properly routed to your new keyspace. Insights will also show you any errors, query performance issues, and more.

If you moved every table and the source keyspace has no tables left, you can delete it with [`pscale keyspace delete`](../../cli/keyspace.md) or from the **Clusters** page.

That’s it! The tables you selected at the beginning are now being served by the new sharded keyspace.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
