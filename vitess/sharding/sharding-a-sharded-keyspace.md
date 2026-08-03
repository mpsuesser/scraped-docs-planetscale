---
url: https://planetscale.com/docs/vitess/sharding/sharding-a-sharded-keyspace
title: "Sharding A Sharded Keyspace"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

Adding or removing shards from a sharded keyspace requires some rebalancing, as you may be moving data from one MySQL instance to another. In PlanetScale, this is done using a sharded to sharded keyspace [workflow](../scaling/workflows.md). This involves creating a new sharded (target) keyspace with the new desired number of shards and transferring all of the data from your current (source) keyspace to the new target. This operation is done with no downtime.

The steps in this documentation are similar to those in the [Sharding quickstart](sharding-quickstart.md), however, there is one important addition that you cannot skip. Be sure to review [Step 7 — Remove `"require_explicit_routing": true`](sharding-a-sharded-keyspace.md#step-7---remove-require_explicit_routing-true), as it is a crucial step in this workflow configuration that differs from the original unsharded to sharded workflow.

- If you are sharding an existing table in an *unsharded* keyspace, follow the instructions in the [Sharding quickstart documentation](sharding-quickstart.md).
- If you are creating a new table that you want in your existing sharded keyspace, follow the instructions in the [Sharding new tables documentation](sharding-new-tables.md).
- If you simply need to adjust the size of each shard, and not the number of shards, you can do so from the [Clusters](../cluster-configuration.md) page in the dashboard.

These are advanced configuration settings that expose some of the underlying Vitess configuration of your cluster. Misconfiguration can cause availability issues. We recommend thoroughly reading through the documentation in the [Sharding section](../sharding.md) of the docs prior to making any changes. If you have any questions, please [reach out to our support team](https://planetscale.com/contact?initial=support).

Throughout this guide, we will refer to the source keyspace and target keyspace, which are defined as follows:

- **Source keyspace** — The original sharded keyspace from which you are moving the tables you wish to shard.
- **Target keyspace** — The new sharded keyspace that you are moving the selected tables to.

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

If you are using [Vitess global routing](https://vitess.io/docs/reference/features/global-routing/) (for example, if you are using `@primary`), you will get ambiguous table errors once you add Vindexes to your new, third keyspace.

To prevent this error, you **must** temporarily add `require_explicit_routing` to your new, second keyspace’s VSchema:

```text
{
  "sharded": true,
  "require_explicit_routing": true,
}
```

This will instruct Vitess’s global routing to exclude your second keyspace from routing until explicitly targeted. This is just temporary. You will remove this later in this tutorial *before* completing the workflow.

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

## Sharding with the sharded to sharded workflow

### Step 1 — Set up the workflow

We are now going to move the tables, data included, from the source keyspace to the new, target one that you created in the pre-sharding checklist. Make sure the “ **Source keyspace** ” dropdown shows your original sharded keyspace and the “ **Destination keyspace** ” shows the new sharded keyspace you made.

### Step 2 - Copying phase

As soon as you click “Create workflow”, we begin the copying phase. During this phase, Vitess is copying rows of the table(s) you’ve selected from your source keyspace to your target keyspace. This uses a combination of `SELECT * FROM TABLE` and binlog-based replication.

There are no active steps for you here besides monitoring the logs at the bottom of the screen in case of errors.

### Step 3 - Verify data consistency

Once the initial data has been copied over, you’ll see this message:

> The source keyspace is currently serving all traffic. Before switching traffic, we need to verify data consistency across keyspaces.

Click “Verify data” to verify the consistency of data between the keyspaces. This step may take a few minutes. Once it’s complete, you should see “Data verified”, meaning you can proceed to the next step.

### Step 4 - Running phase

Assuming there were no errors in the previous stage, you will have automatically entered the running phase — pure binlog-based replication. This also means replication lag was low enough for VReplication to advance into this phase. You should also see `State Changed: running` in the logs below.

During this phase, the following happens:

- Your source keyspace is still serving all primary and replica traffic for the tables you’re moving over.
- All existing data that is going to the target keyspace has been copied over.
- VReplication is also replicating all new incoming writes to the tables in the target keyspace.

Again, you should check the logs below to ensure there are no errors and to better understand the ongoing workflow process. There are no active steps to take during this phase. If the logs do not show any errors, you can proceed to the next step.

### Step 5 - Switch traffic to target keyspace

You are now able to switch the traffic over so that traffic to the sharded tables is served from the target keyspace instead of the source keyspace.

You have two options here:

1. Switch both primary and replica traffic.
2. Switch just replica traffic.

If you want to test the replica traffic only first, you can select “ **Switch replica traffic only** ” from the dropdown, and then click the button. Otherwise, click “ **Switch primary and replica traffic** ”.

### Step 6 - Check traffic in your application

You should now go check out your production application that uses this database to make sure everything is running as expected.

If you selected to only switch replica traffic in the previous step and data that is being served from replicas in your production application looks good, you can click “ **Switch primary traffic** ” when you’re ready. Again, go to your production application to make sure everything is working as expected.

During this phase, you can also go to your “ **Insights** ” tab in the dashboard to see markers showing where your workflow started and transitioned into different states. If something looks off where you see a marker, it is worth investigating.

You might notice during this phase that you also have the option to “ **Undo traffic switch** ”. So if you do notice an issue once you switched to serve traffic from the target keyspace, you can click this button to revert back to serving traffic from the source keyspace. Remember, both keyspaces have a copy of the same data for the targeted tables, as described in the Running phase above.

### Step 7 - Remove "require\_explicit\_routing": true

You should have added `require_explicit_routing` to your target keyspace’s VSchema in step 4 of the “Pre-sharding checklist”:

```text
{
  "require_explicit_routing": true,
  ...
}
```

You’ll need to remove it before completing the workflow.

If you don’t remove this, you may start to see “table not found” errors once the workflow is completed.

### Step 8 - Complete the workflow

Please note, up until now, you’ve had the option to click “Cancel workflow” in the top right corner. Once you click “Complete workflow” in this step, there is no going back. You will have the option to reinstate the routing rules if it appears your queries aren’t being routed correctly, but you cannot swap the tables back to the source keyspace.

At this stage, you have the option to reverse the traffic if you need more time to test. This will switch you back to serving from the source keyspace (see step 4).

If you have removed `"require_explicit_routing": true` from your target keyspace VSchema, and you are sure you want to proceed with this operation, you can click “ **Complete workflow** ”. This action is irreversible.

### Step 9 - Check that your production application is working as expected

Finally, check your production application to make sure everything is working as expected. You can check your [Insights](../monitoring/query-insights.md) tab to see if queries are being properly routed to your new keyspace. Insights will also show you any errors, query performance issues, and more.

If you realize there are issues, such as queries not being correctly served to the new keyspace, you can click “My application has errors”, and we will temporarily restore the routing rules. If you are facing errors, double check that you have removed `"require_explicit_routing": true` from your target keyspace VSchema. Once your application is updated, click “ **I have updated my application** ”.

Once everything looks good, click “ **My application is working** ”, and the workflow will complete.

That’s it! The tables you selected at the beginning are now being served by the new sharded keyspace.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
