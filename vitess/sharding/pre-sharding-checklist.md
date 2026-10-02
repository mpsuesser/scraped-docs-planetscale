---
url: https://planetscale.com/docs/vitess/sharding/pre-sharding-checklist
title: "Pre Sharding Checklist"
description: ""
access_date: 2026-10-02T19:13:10.531Z
current_date: 2026-10-02T19:13:10.531Z
---

If you create your workflow with `--sharded-auto-increment-handling REPLACE` and `--global-keyspace`, as in the [Sharding quickstart](sharding-quickstart.md), MoveTables removes `AUTO_INCREMENT` from the target tables and creates the sequence tables for you. You can skip this page.

Follow the steps below if you would rather create and name the sequence tables yourself.

## Remove AUTO\_INCREMENT from the sharded tables

When a table is spread across multiple shards, using `AUTO_INCREMENT` on your primary key can cause problems. Because each shard is its own separate MySQL instance, the shards do not have the context to know whether or not a primary key for a table entry is already in use on other shards. This means you risk two different table entries being assigned the same primary key.

To avoid this, it is a best practice to use [sequence tables](sequence-tables.md) instead.

When you create the workflow, MoveTables copies the schema of each table you’re moving to the target keyspace. By default (`--sharded-auto-increment-handling REMOVE`), it removes `AUTO_INCREMENT` from those tables as it copies them, so you don’t need to create the target tables yourself. The rest of this page sets up the sequence tables that replace it. This example moves the `users` and `notifications` tables.

## Add sequence tables to unsharded keyspace

As mentioned earlier, you should use [sequence tables](sequence-tables.md) in place of `AUTO_INCREMENT` for your sharded tables.

Your sequence tables will live in the source unsharded keyspace.

## Add the sequence tables to the VSchema

The following will add the sequence tables to the source keyspace VSchema (`metal`):

```sql
alter vschema add sequence \`metal\`.notifications_seq;
alter vschema add sequence \`metal\`.users_seq;
```

Next, add the following to specify that those sequence tables should be used as the sequence tables for the sharded tables in the new target keyspace VSchema (`metal-sharded`):

```sql
alter vschema on metal-sharded.notifications add auto_increment id using \`metal\`.notifications_seq;
alter vschema on metal-sharded.users add auto_increment id using \`metal\`.users_seq;
```

The resulting VSchema for `metal` will look like this:

```json
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

## Add the tables to the source keyspace VSchema (metal)

If you are using Vitess global routing you may have already completed this. If so, you can skip this step.

You now need to add all tables to your source keyspace (`metal` for this example) VSchema. The VSchema is used to route queries to the proper keyspace. When you only had one keyspace, you didn’t need to worry about this. But now that you’ve added a new sharded keyspace, Vitess will need to check the VSchema of each keyspace to route queries.

For more information, see the [VSchema documentation](vschema.md).

For this step, it’s often easier to do from the UI instead of with an `ALTER` statement.

## Initialize the sequences when you switch traffic

When you switch primary traffic, pass `--initialize-target-sequences` so that each sequence table starts above the highest ID already in its table:

```shellscript
pscale branch vtctl move-tables switch-traffic <DATABASE_NAME> <BRANCH_NAME> \
  --workflow <WORKFLOW_NAME> \
  --target-keyspace metal-sharded \
  --tablet-types PRIMARY \
  --initialize-target-sequences
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
