---
url: https://planetscale.com/docs/vitess/sharding/pre-sharding-checklist
title: "Pre Sharding Checklist"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

If you started a workflow while following the [Sharding quickstart](sharding-quickstart.md) and saw a lot of incomplete steps in the validation phase, you need follow the instructions in this document, and then go back to the quickstart to continue the workflow.

If you did not get those warnings, you’re on a newer Vitess branch, and you do not need to take any action here.

## Copy the sharded tables to the new keyspace and remove AUTO\_INCREMENT

When you begin the workflow, we first copy the schema(s) of the table(s) you wish to shard over to the specified target keyspace. However, there is an intermediate step here: remove any existing `AUTO_INCREMENT` s on the primary key for these table(s).

When a table is spread across multiple shards, using `AUTO_INCREMENT` on your primary key can cause problems. Because each shard is its own separate MySQL instance, the shards do not have the context to know whether or not a primary key for a table entry is already in use on other shards. This means you risk two different table entries being assigned the same primary key.

To avoid this, it is a best practice to use [sequence tables](sequence-tables.md) instead. We will cover how to set these up shortly. First, let’s remove `AUTO_INCREMENT` from the tables you’re sharding:

1. Make a copy of the table schema(s) that are moving to this new keyspace. Leave off `AUTO_INCREMENT` if it previously existed. For this example, we’ll create the `users` and `notifications` tables that will live on that keyspace.

Besides dropping `AUTO_INCREMENT`, the table schema must match exactly what you have on your original source keyspace. To quickly grab the SQL to create the table, you can go to your “Branches” tab in the dashboard, click your main branch, click the table you need to copy over, and copy the `CREATE TABLE` SQL. Again, make sure to remove `AUTO_INCREMENT`.

```sql
CREATE TABLE \`users\` ( \`id\` bigint NOT NULL, \`name\` varchar(255) NOT NULL, \`email\` varchar(255) NOT NULL, \`password\` varchar(255) NOT NULL, PRIMARY KEY (\`id\`), UNIQUE KEY \`index_users_on_email\` (\`email\`) ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

CREATE TABLE \`notifications\` ( \`id\` bigint NOT NULL, \`content\` varchar(255) NOT NULL, \`user_id\` bigint NOT NULL, \`created_at\` datetime(6) NOT NULL, \`updated_at\` datetime(6) NOT NULL, PRIMARY KEY (\`id\`), KEY \`index_notifications_on_user_id\` (\`user_id\`), KEY \`index_notifications_on_user_id_and_created_at\` (\`user_id\`,\`created_at\`) ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
```

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

For more infomation, see the [VSchema documentation](vschema.md).

For this step, it’s often easier to do from the UI instead of with an `ALTER` statement.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
