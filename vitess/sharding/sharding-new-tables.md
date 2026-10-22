---
url: https://planetscale.com/docs/vitess/sharding/sharding-new-tables
title: "Sharding New Tables"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Sharding new tables

> This tutorial shows you how to create new tables in a sharded keyspace. If you have an existing table that you want to shard, follow the [Sharding quickstart](sharding-quickstart.md) instead.

> [!WARNING]
>
> Misconfiguration can cause availability issues. We recommend thoroughly reading through the documentation in the [Sharding section](https://planetscale.com/docs/vitess/sharding.md) of the docs prior to making any changes. If you have any questions, please [reach out to our support team](https://planetscale.com/contact?initial=support).

Before you begin, we recommend the following reading:

> **[What is a keyspace?](https://planetscale.com/docs/vitess/sharding.md)**

> **[Vindexes](https://planetscale.com/docs/vitess/sharding/vindexes.md)**

> **[Sharding quickstart](https://planetscale.com/docs/vitess/sharding/sharding-quickstart.md)**

> **[Avoiding cross-shard queries](https://planetscale.com/docs/vitess/sharding/avoiding-cross-shard-queries.md)**

> **[Sequence tables](https://planetscale.com/docs/vitess/sharding/sequence-tables.md)**

> [!NOTE]
>
> Sharded keyspaces are not supported on databases with foreign key constraints enabled.

When adding a new table to your database cluster, you have the option to shard it from the start. This can be a good option if you expect the table to grow incredibly quickly, or if you already have some other sharded tables that you know you will frequently join with this new table and want to avoid cross-shard queries.

Before you get started, you need a sharded keyspace. If you already have one, you can skip the next section.

## 1. Add a sharded keyspace

> [!WARNING]
>
> If you are using [Vitess global routing](https://vitess.io/docs/reference/features/global-routing/), you must take extra care when adding a sharded keyspace to your database (for example, if you are using `@primary`).
> Before creating one, you must ensure that all tables from your first *unsharded keyspace* are added to the `VSchema` of that *unsharded keyspace*. Eg:
>
> ```
> {
>   "tables": {
>     "users": { }
>     ...
>   }
> }
> ```
>
> Otherwise, queries will fail. [Learn more about VSchema.](https://planetscale.com/docs/vitess/sharding/vschema.md)

Navigate to the [Clusters page](https://planetscale.com/docs/vitess/cluster-configuration.md).

**Step 1**

Click "New keyspace".

**Step 2**

Enter the keyspace name. For example, if your existing unsharded keyspace is named `metal`, you may create a sharded keyspace named `metal-sharded`.

**Step 3**

Select the number of shards you want to create in this keyspace.

> [!NOTE]
>
> The cost of adding this additional keyspace largely depends on the number of shards you choose, the cluster size, and if you'd like to add additional replicas.

**Step 4**

Choose the cluster sizes you would like to use for this keyspace. Keep in mind, if you are creating a sharded keyspace, this will spin up multiple clusters of the selected size. For example, if you are creating 4 shards and choose the `PS-80` cluster size, we will create 4 `PS-80`s, each with 1 primary and 2 replicas.

**Step 5**

Select the number of *additional* replicas, if any, that you'd like to add to each cluster. Each cluster comes with 2 replicas by default, so any number you choose will be in addition to those 2.

**Step 6**

Review the new monthly cost for this keyspace below. This is in addition to your existing unsharded keyspace, as well as any other keyspaces you add.

**Step 7**

Once satisfied, click "Create keyspace".

## 2. Copy the sharded tables to the new keyspace and remove `AUTO_INCREMENT`

Without considering sharding, you may create a new table that looks like this:

```sql theme={null}
-- Don't create a sharded table with `AUTO_INCREMENT` on the primary key
CREATE TABLE exercise_logs (
  id BIGINT UNSIGNED AUTO_INCREMENT, -- Problem!
  user_id BIGINT UNSIGNED,
  exercise_id BIGINT UNSIGNED,
  created_at DATETIME,
  reps SMALLINT UNSIGNED,
  sets SMALLINT UNSIGNED,
  weight SMALLINT UNSIGNED,
  notes VARCHAR(1024),
  PRIMARY KEY(id)
);
```

When a table is spread across multiple shards, using `AUTO_INCREMENT` on your primary key can cause problems. Because each shard is its own separate MySQL instance, the shards do not have the context to know whether or not a primary key for a table entry is already in use on other shards. This means you risk two different table entries being assigned the same primary key.

To avoid this, it is a best practice to use [sequence tables](https://planetscale.com/docs/vitess/sharding/sequence-tables.md) instead. We will cover how to set these up shortly. First, make sure you remove any `AUTO_INCREMENT`s from the tables you're sharding.

```sql theme={null}
-- Switch to the new keyspace
USE `metal-sharded`;

-- Create the table with no `AUTO_INCREMENT`
CREATE TABLE exercise_logs (
  id BIGINT UNSIGNED, -- Better!
  user_id BIGINT UNSIGNED,
  exercise_id BIGINT UNSIGNED,
  created_at DATETIME,
  reps SMALLINT UNSIGNED,
  sets SMALLINT UNSIGNED,
  weight SMALLINT UNSIGNED,
  notes VARCHAR(1024),
  PRIMARY KEY(id)
);
```

## 3. Add sequence tables to unsharded keyspace

As mentioned earlier, you should use [sequence tables](https://planetscale.com/docs/vitess/sharding/sequence-tables.md) in place of `AUTO_INCREMENT` for your sharded tables.

Your sequence tables will live in the source unsharded keyspace.

**Step 1**

Switch back to your original unsharded keyspace.

```sql theme={null}
USE `metal`;
```

**Step 2**

Create the new sequence table for `exercise_logs`. If you're adding multiple new tables, create a sequence table for each:

```sql theme={null}
CREATE TABLE exercise_logs_seq (
  id BIGINT,
  next_id BIGINT,
  cache BIGINT,
  PRIMARY KEY(id)
  -- 👇 Comment is required
) COMMENT='vitess_sequence'; 
```

> [!TIP]
>
> Note `COMMENT='vitess_sequence'` at the end. This must be added for every sequence table you create.

## 4. Choose your Vindexes

Every sharded table needs a [Vindex](https://planetscale.com/docs/vitess/sharding/vindexes.md) (Vitess index) to determine how incoming rows are distributed across shards. Similar to how every MySQL table must have a primary key, every sharded table must additionally have a **primary Vindex**.

The primary Vindex determines which shard each row of data will reside on. For more information about choosing a Vindex, see the [Vindexes documentation](https://planetscale.com/docs/vitess/sharding/vindexes.md). You can also see a practical example in the [Avoiding cross-shard queries](https://planetscale.com/docs/vitess/sharding/avoiding-cross-shard-queries.md) documentation.

In this example, we'll shard `exercise_logs` by `user_id` using the predefined [`xxhash` Vindex function](https://vitess.io/docs/reference/features/vindexes/#predefined-vindexes), which is a common choice:

```sql theme={null}
ALTER VSCHEMA ON `metal-sharded`.exercise_logs ADD VINDEX xxhash(user_id) USING xxhash;
```

This registers the table in the sharded keyspace's VSchema and configures how rows are distributed across shards.

## 5. Add the sequence tables to the VSchema

Now that the table exists in the sharded keyspace's VSchema, you can configure the sequence tables.

The following will add the sequence table to the source keyspace VSchema (`metal`):

```sql theme={null}
ALTER VSCHEMA ADD SEQUENCE `metal`.exercise_logs_seq;
```

Next, add the following to specify that the sequence table should be used for the sharded table's primary key in the target keyspace VSchema (`metal-sharded`):

```sql theme={null}
ALTER VSCHEMA ON `metal-sharded`.exercise_logs ADD AUTO_INCREMENT id USING `metal`.exercise_logs_seq;
```

### Verify with pscale CLI

You can verify the resulting VSchema for each keyspace with the [`pscale` CLI](../../cli/keyspace.md).

Check the `metal` keyspace VSchema:

```bash theme={null}
pscale keyspace vschema show <DATABASE_NAME> <BRANCH_NAME> metal
```

```json theme={null}
{
  "tables": {
    "exercise_logs_seq": {
      "type": "sequence"
    }
  }
}
```

Check the `metal-sharded` keyspace VSchema:

```bash theme={null}
pscale keyspace vschema show <DATABASE_NAME> <BRANCH_NAME> metal-sharded
```

```json theme={null}
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
        "sequence": "metal.exercise_logs_seq"
      }
    }
  }
}
```

The `column_vindexes` entry tells Vitess to use the `xxhash` Vindex on the `user_id` column to determine shard placement. The `auto_increment` entry configures the sequence table for generating unique primary keys across shards.

## 6. Add the tables to the source keyspace VSchema (`metal`)

You now need to add all tables to your source keyspace (`metal` for this example) VSchema. The VSchema is used to route queries to the proper keyspace. When you only had one keyspace, you didn't need to worry about this. But now that you've added a new sharded keyspace, Vitess will need to check the VSchema of each keyspace to route queries.

For more information, see the [VSchema documentation](https://planetscale.com/docs/vitess/sharding/vschema.md).

For this step, it's often easier to do from the UI instead of with an `ALTER` statement.

**Step 1**

On the Clusters page, click on your source unsharded keyspace (`metal`).

**Step 2**

Select the branch you created in the previous step.

**Step 3**

Click "VSchema".

**Step 4**

Add in **all tables** that exist in this keyspace. This is what our `metal` keyspace looks like:

```json theme={null}
{
  "tables": {
    "exercises": {},
    "exercise_log": {},
    "programs": {},
    "users": {}
  }
}
```

**Step 5**

Click "Save changes"

## 7. Targeting the correct keyspace

Once you have more than one keyspace, with tables distributed across both keyspaces, your application may not know how to properly route queries to the correct keyspace.

If you originally set up your application configuration code with something like `DATABASE_NAME=your_database_name`, where `your_database_name` is the name of your original unsharded keyspace, you will need to update your configuration code so that all queries don't go straight to that keyspace.

The preferred way to do this is to just leave off the database name completely in your application configuration code. PlanetScale will be able to route traffic correctly just using the connection username and password.

While this is the preferred way, it's sometimes not possible. For example, many frameworks and ORMs require that you include a database name.

In those cases, you should use `@primary`. This will send any incoming queries first to our [Global Edge Network](https://planetscale.com/blog/introducing-global-replica-credentials#building-planetscale-global-network), which will see that you're targeting a primary. Edge will then send the request to the VTGate(s)/load balancer. We typically will use [Vitess's Global Routing](https://vitess.io/docs/reference/features/global-routing/) to direct the query to the correct keyspace and, optionally, correct shard.

> [!NOTE]
>
> If you explicitly wish to target a replica for some or all reads, using `@replica` will have the same effect as `@primary` in that it will automatically route the request to the correct keyspace.

[Global Replica Credentials](https://planetscale.com/docs/vitess/scaling/replicas.md#1-create-a-global-replica-credential-recommended) are not currently supported in this context. You can still target replicas instead of your primary with `@replica`, but it will not automatically route the query to the *closest* replica.

For more information, refer to the [Targeting the correct keyspace documentation](https://planetscale.com/docs/vitess/sharding/targeting-correct-keyspace.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
