---
url: https://planetscale.com/docs/vitess/sharding/sharding-quickstart
title: "Sharding Quickstart"
description: ""
access_date: 2026-10-02T19:13:10.531Z
current_date: 2026-10-02T19:13:10.531Z
---

- If you are creating a new table that you want in a sharded keyspace, follow the instructions in the [Sharding new tables doc](sharding-new-tables.md).
- If you are moving tables from one sharded keyspace to another sharded keyspace, follow the instructions in the [Sharding a sharded keyspace doc](sharding-a-sharded-keyspace.md).

Before you begin, we recommend the following reading:

## Workflows overview

## What is a keyspace?

## Vindexes

## Avoiding cross-shard queries

Sharded keyspaces are not supported on databases with foreign key constraints enabled.

These are advanced configuration settings that expose some of the underlying Vitess configuration of your cluster. Misconfiguration can cause availability issues. We recommend thoroughly reading through the documentation in the of the docs prior to making any changes. If you have any questions, please [reach out to our support team](https://planetscale.com/contact?initial=support).

Throughout this guide, we will refer to the source keyspace and target keyspace, which are defined as follows:

- **Source keyspace**: The original unsharded keyspace from which you are moving the tables you wish to shard.
- **Target keyspace**: The new sharded keyspace that you are moving the selected tables to.
- **Global keyspace**: An unsharded keyspace that holds the [sequence tables](sequence-tables.md) that replace `AUTO_INCREMENT` on the sharded tables. This is usually the source keyspace.

You run the move itself with [`pscale branch vtctl move-tables`](../../cli/move-tables.md). Make sure you have the [`pscale` CLI installed and authenticated](../../cli/planetscale-environment-setup.md) before you start. You must be an [Organization Administrator](../../security/access-control.md#organization-administrator) or a [Database Administrator](../../security/access-control.md#database-administrator) of the database to create and change workflows.

## Prepare to shard

There is a small amount of upfront work that needs to happen before you move your table(s).

### 1\. Decide which table(s) you want to shard

First and foremost, decide which table(s) you want to shard. Some common signals that a table may benefit from sharding include:

- The table has become very large (>100 GB) and query performance has degraded due to this
- Schema changes to the table take hours
- You expect the table to grow quickly and want to shard it before it becomes a problem

### 2\. Identify tables you frequently JOIN

Once you know the tables that you are going to move to a sharded keyspace, you also need to think about which other tables you frequently join with the tables you are going to shard. We recommend that tables you frequently join together all live in the same keyspace.

As an example, if you have an `exercise_logs` table that has become extremely large and continues to grow, you may decide to move this to a sharded keyspace. Perhaps this table is frequently joined with the `users` table. In this scenario, we recommend moving both the `exercise_logs` and `users` tables to the new sharded keyspace and sharding both tables.

The goal here is to avoid cross-keyspace or cross-shard queries. For more information about this, see the [Avoiding cross-shard queries](avoiding-cross-shard-queries.md) documentation.

### 3\. Create a sharded keyspace

If you are using [Vitess global routing](https://vitess.io/docs/reference/features/global-routing/) (for example, if you are using `@primary`), you must take extra care when adding a second keyspace to a database. Before creating one, you must ensure that all tables from your *initial keyspace* are added to the `VSchema` of that *initial keyspace*. Eg:

```text
{
  "tables": {
    "users": { }
    ...
  }
}
```

Otherwise, queries will fail. [Learn more about VSchema.](vschema.md)

To set up the sharded keyspace:

You can also create the keyspace with [`pscale keyspace create`](../../cli/keyspace.md):

```shellscript
pscale keyspace create <DATABASE_NAME> main metal-sharded --shards 4 --cluster-size PS_80 --wait
```

### 4\. Choose your Vindexes

If you are using [Vitess global routing](https://vitess.io/docs/reference/features/global-routing/) (for example, if you are using `@primary`), you may get ambiguous table errors once you add Vindexes to your new, second keyspace, if those tables also exist in the first keyspace.

To prevent this error, you can add `require_explicit_routing` to your new, second keyspace’s VSchema:

```text
{
  "require_explicit_routing": true,
  ...
}
```

This will instruct Vitess’s global routing to exclude your second keyspace from routing until explicitly targeted. Make sure to remove this field *before* completing the workflow.

When configuring a sharded keyspace, you must think about *how* to distribute the data across shards. This is done by selecting a [Vindex](vindexes.md) (Vitess index) for each table.

A Vindex provides a way to map incoming rows of data to the appropriate shard in your keyspace. Similar to how every MySQL table must have a primary key, every sharded table must additionally have a **primary Vindex**.

The primary Vindex is the Vindex that determines which shard each row of data will reside on. For more information about choosing a Vindex, see the [Vindexes documentation](vindexes.md). You can also see an example in the [Avoiding cross-shard queries](avoiding-cross-shard-queries.md) documentation.

To specify the vindex for the tables you want to shard:

For example, let’s say we are sharding a table called `exercise_logs`, and we determined `user_id` to be the best option. We are also using the predefined [`xxhash` Vindex function](https://vitess.io/docs/reference/features/vindexes/#predefined-vindexes), which is a common choice.

```sql
ALTER VSCHEMA ON exercise_logs ADD VINDEX xxhash(user_id) USING xxhash;
ALTER VSCHEMA ON users ADD VINDEX xxhash(id) USING xxhash;
```

You do not need to create sequence tables yourself. MoveTables creates them in the global keyspace when you create the workflow in the next section. See the [pre-sharding checklist](pre-sharding-checklist.md) if you would rather set them up by hand.

### 5\. Deploy the changes to production

Once you’re finished with these pre-sharding steps, you can go ahead and deploy the changes to production.

If you go back to your Clusters tab, click your sharded keyspace, and click the VSchema tab, you’ll see those changes reflected there.

## Move the tables with MoveTables

Now that the prep work is done, it’s time to move the tables you chose into your sharded keyspace. The examples below use a database named `mydb`, the `main` production branch, the unsharded `metal` keyspace, and the new `metal-sharded` keyspace.

### Step 1: Create the workflow

```shellscript
pscale branch vtctl move-tables create mydb main \
  --workflow shard_users \
  --source-keyspace metal \
  --target-keyspace metal-sharded \
  --tables users,exercise_logs \
  --sharded-auto-increment-handling REPLACE \
  --global-keyspace metal \
  --defer-secondary-keys \
  --format json
```

This creates a MoveTables workflow named `shard_users` and starts it right away. You will use the workflow name and the target keyspace in every later command.

- `--sharded-auto-increment-handling REPLACE` removes `AUTO_INCREMENT` from the target tables and replaces it with Vitess [sequence tables](sequence-tables.md). Because you passed `--global-keyspace metal`, MoveTables creates those sequence tables in the `metal` keyspace.
- `--defer-secondary-keys` creates the target tables’ secondary indexes after the copy finishes instead of during it, which makes the copy much faster.

By default, the workflow stops if a schema change runs on a moved table in the source keyspace while the workflow is running, so you can review it before continuing. See [`move-tables`](../../cli/move-tables.md) for `--on-ddl` and the other options.

### Step 2: Watch the copy

As soon as the workflow starts, Vitess copies rows of the tables you selected from the source keyspace to the target keyspace. It uses a combination of `SELECT * FROM table` and binlog-based replication.

Check progress with `status`:

```shellscript
pscale branch vtctl move-tables status mydb main \
  --workflow shard_users \
  --target-keyspace metal-sharded \
  --format json
```

While the copy is running, `table_copy_state` lists each table’s progress. Once every table is copied, the streams move to `Running`: Vitess keeps replicating every new write on the source tables to the target keyspace. At this point:

- Your source keyspace is still serving all primary and replica traffic for the tables you’re moving.
- All existing data has been copied to the target keyspace.
- VReplication keeps the target tables up to date with new writes.

You can also follow the workflow from the dashboard. Click “ **Workflows** ” in the left nav, select your branch, and open the workflow to see its streams, per-table copy progress, replication lag, and traffic routing. The dashboard view is read-only: you drive every step from the CLI.

Each command’s JSON output includes a `next_steps` field with the command to run next.

### Step 3: Verify data consistency

Once the streams are `Running`, verify that the source and target keyspaces hold the same data with a [VDiff](https://vitess.io/docs/reference/vreplication/vdiff/):

```shellscript
pscale branch vtctl vdiff create mydb main \
  --workflow shard_users \
  --target-keyspace metal-sharded \
  --format json
```

The output includes the VDiff’s `uuid`. Pass it to `vdiff show` and repeat until the VDiff completes:

```shellscript
pscale branch vtctl vdiff show mydb main \
  --workflow shard_users \
  --target-keyspace metal-sharded \
  --uuid <VDIFF_UUID> \
  --format json
```

If the VDiff reports mismatches, do not switch traffic until you have resolved them. VDiff is optional, but we recommend running it before you switch traffic on a production branch.

### Step 4: Switch traffic to the target keyspace

You are now able to switch traffic so that the moved tables are served from the target keyspace instead of the source keyspace. You can switch replica traffic first to test reads, then switch primary traffic.

Switch replica traffic:

```shellscript
pscale branch vtctl move-tables switch-traffic mydb main \
  --workflow shard_users \
  --target-keyspace metal-sharded \
  --tablet-types REPLICA,RDONLY \
  --format json
```

Once reads from replicas look good in your application, switch primary traffic:

```shellscript
pscale branch vtctl move-tables switch-traffic mydb main \
  --workflow shard_users \
  --target-keyspace metal-sharded \
  --tablet-types PRIMARY \
  --initialize-target-sequences \
  --format json
```

`--initialize-target-sequences` sets each sequence table’s next value above the highest ID already in the table, so new rows don’t collide with copied rows. Pass `--dry-run` to either command to see what would happen without switching.

After primary traffic switches, Vitess starts a reverse workflow that replicates writes from the target keyspace back to the source keyspace. Both keyspaces keep the same data until you complete the workflow.

### Step 5: Check traffic in your application

You should now go check out your production application that uses this database to make sure everything is running as expected. You can also check the [Insights](../monitoring/query-insights.md) tab in the dashboard for errors or slow queries on the moved tables.

If something looks wrong, switch traffic back to the source keyspace with `reverse-traffic`:

```shellscript
pscale branch vtctl move-tables reverse-traffic mydb main \
  --workflow shard_users \
  --target-keyspace metal-sharded \
  --format json
```

You can reverse and switch traffic again as many times as you need until you complete the workflow.

### Step 6: Update your application code to serve from @primary

When you switched traffic, Vitess applied [schema routing rules](https://vitess.io/docs/reference/features/schema-routing-rules/). Routing rules are responsible for routing traffic to the correct keyspace and/or shard.

The configuration code in your application likely says something like `database_name = your_database_name`, where `your_database_name` is your original unsharded keyspace. This was fine when you only had one keyspace, but now that you have multiple keyspaces, your application won’t know that the other ones exist with this current configuration. The routing rules applied during this workflow point incoming queries from your unsharded keyspace to the sharded keyspace, where necessary.

However, when you complete the workflow in the next step with `--keep-routing-rules=false`, **these routing rules are removed**. That means if your application is still configured to explicitly send traffic to your original unsharded keyspace, `database_name = your_database_name`, Vitess will not know how to correctly route the queries for the tables that moved to the sharded keyspace.

The fix for this is simple: update your application to route traffic to your primary instance. You can do that by setting database name to `@primary`.

For example, in Rails, it would look like this:

```yaml
# database.yml
production:
  <<: *default
  username: <%= Rails.application.credentials.planetscale&.fetch(:username) %>
  password: <%= Rails.application.credentials.planetscale&.fetch(:password) %>
  database: "@primary"
  host: <%= Rails.application.credentials.planetscale&.fetch(:host) %>
  ssl_mode: verify_identity
```

You can safely update and deploy this application code before completing the workflow. When you only have one keyspace, `@primary` will automatically route queries to that keyspace. Likewise, if you have queries that you specifically send to replicas or read-only regions, you can use `@replica` for those to have them automatically routed to the correct keyspace/shard.

For more framework-specific examples, see [Targeting the correct keyspace documentation](targeting-correct-keyspace.md).

Once you deploy this code change, double check that everything in your production application is working correctly.

### Step 7: Complete the workflow

If you added `require_explicit_routing` to your target keyspace’s VSchema in step 4:

```text
{
  "require_explicit_routing": true,
  ...
}
```

You’ll need to remove it before completing the workflow.

Before you proceed with this step, **it is extremely important that you complete step 6**. This requires changes to your application code.

Up until now, you can cancel the workflow or reverse traffic. Completing the workflow is not reversible: it stops replication between the keyspaces and, with the flags below, drops the moved tables from the source keyspace and removes the routing rules. Preview what `complete` will do with `--dry-run` first:

```shellscript
pscale branch vtctl move-tables complete mydb main \
  --workflow shard_users \
  --target-keyspace metal-sharded \
  --keep-data=false \
  --keep-routing-rules=false \
  --dry-run \
  --format json
```

Once you have reviewed the output, run the same command without `--dry-run`. `--keep-data` and `--keep-routing-rules` are required, so you always choose what happens to the source tables and routing rules:

- `--keep-data=false` drops the moved tables from the source keyspace. Add `--rename-tables` to rename them instead of dropping them. `--keep-data=true` leaves them in place.
- `--keep-routing-rules=false` removes the routing rules. Pass `--keep-routing-rules=true` to keep them if some queries still name the source keyspace.

Write boolean flags as `--keep-data=false`, with an `=`. A space-separated value such as `--keep-data false` is read as `--keep-data=true`.

### Step 8: Check that your production application is working as expected

Finally, check your production application to make sure everything is working as expected. You can check your [Insights](../monitoring/query-insights.md) tab to see if queries are being properly routed to your new keyspace. Insights will also show you any errors, query performance issues, and more.

That’s it! The tables you selected at the beginning are now being served by the sharded keyspace.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
