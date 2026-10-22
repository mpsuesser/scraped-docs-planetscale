---
url: https://planetscale.com/docs/vitess/sharding/pre-sharding-checklist
title: "Pre Sharding Checklist"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Pre-sharding checklist

> Replace `AUTO_INCREMENT` columns with sequence tables by hand before you move tables from an unsharded keyspace to a sharded keyspace.

> [!NOTE]
>
> If you create your workflow with `--sharded-auto-increment-handling REPLACE` and `--global-keyspace`, as in the [Sharding quickstart](https://planetscale.com/docs/vitess/sharding/sharding-quickstart.md), MoveTables removes `AUTO_INCREMENT` from the target tables and creates the sequence tables for you. You can skip this page.
>
> Follow the steps below if you would rather create and name the sequence tables yourself.

## Remove `AUTO_INCREMENT` from the sharded tables

When a table is spread across multiple shards, using `AUTO_INCREMENT` on your primary key can cause problems. Because each shard is its own separate MySQL instance, the shards do not have the context to know whether or not a primary key for a table entry is already in use on other shards. This means you risk two different table entries being assigned the same primary key.

To avoid this, it is a best practice to use [sequence tables](https://planetscale.com/docs/vitess/sharding/sequence-tables.md) instead.

When you create the workflow, MoveTables copies the schema of each table you're moving to the target keyspace. By default (`--sharded-auto-increment-handling REMOVE`), it removes `AUTO_INCREMENT` from those tables as it copies them, so you don't need to create the target tables yourself. The rest of this page sets up the sequence tables that replace it. This example moves the `users` and `notifications` tables.

## Add sequence tables to unsharded keyspace

As mentioned earlier, you should use [sequence tables](https://planetscale.com/docs/vitess/sharding/sequence-tables.md) in place of `AUTO_INCREMENT` for your sharded tables.

Your sequence tables will live in the source unsharded keyspace.

**Step 1**

Switch back to your original unsharded keyspace.

```sql theme={null}
use `metal`;
```

**Step 2**

Create 2 new sequence tables: one for `notifications` and one for `users`.

```sql theme={null}
CREATE TABLE `notifications_seq` ( `id` bigint NOT NULL, `next_id` bigint DEFAULT NULL, `cache` bigint DEFAULT NULL, PRIMARY KEY (`id`) ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='vitess_sequence';

CREATE TABLE `users_seq` ( `id` bigint NOT NULL, `next_id` bigint DEFAULT NULL, `cache` bigint DEFAULT NULL, PRIMARY KEY (`id`) ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='vitess_sequence';
```

## Add the sequence tables to the VSchema

The following will add the sequence tables to the source keyspace VSchema (`metal`):

```sql theme={null}
alter vschema add sequence `metal`.notifications_seq;
alter vschema add sequence `metal`.users_seq;
```

Next, add the following to specify that those sequence tables should be used as the sequence tables for the sharded tables in the new target keyspace VSchema (`metal-sharded`):

```sql theme={null}
alter vschema on metal-sharded.notifications add auto_increment id using `metal`.notifications_seq;
alter vschema on metal-sharded.users add auto_increment id using `metal`.users_seq;
```

The resulting VSchema for `metal` will look like this:

```json theme={null}
{
  "tables": {
    "notifications_seq": {
      "type": "sequence"
    },
    "users_seq": {
      "type": "sequence"
    }
  }
}
```

## Add the tables to the source keyspace VSchema (`metal`)

> [!NOTE]
>
> If you are using Vitess global routing you may have already completed this.
> If so, you can skip this step.

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

## Initialize the sequences when you switch traffic

When you switch primary traffic, pass `--initialize-target-sequences` so that each sequence table starts above the highest ID already in its table:

```bash theme={null}
pscale branch vtctl move-tables switch-traffic <DATABASE_NAME> <BRANCH_NAME> \
  --workflow <WORKFLOW_NAME> \
  --target-keyspace metal-sharded \
  --tablet-types PRIMARY \
  --initialize-target-sequences
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
