---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/unsupported-sharded-feature
title: "Unsupported Sharded Feature"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# UNSUPPORTED_SHARDED_FEATURE schema lint error

> Fix features that are not supported in sharded PlanetScale Vitess keyspaces.

**Platform availability:** Vitess only

## What this error means

`UNSUPPORTED_SHARDED_FEATURE` means the schema uses a feature that is valid in some MySQL or unsharded Vitess schemas but not in a sharded keyspace.

Common messages include:

```text theme={null}
`recent_orders` is a view: sharded databases cannot have views
table "orders" has an autoincrement column "id": sharded databases cannot have autoincrement columns, use a sequence instead
```

## Why PlanetScale rejects it

In a sharded keyspace, rows are distributed across multiple MySQL instances. Features that require one local MySQL instance to understand the entire table, such as `AUTO_INCREMENT`, cannot coordinate safely across shards. Views are also not supported in sharded keyspaces.

## How to fix it

For `AUTO_INCREMENT` columns, use a Vitess sequence table instead:

```sql theme={null}
CREATE TABLE orders (
  id bigint unsigned NOT NULL,
  customer_id bigint unsigned NOT NULL,
  PRIMARY KEY (id)
);
```

Then create a sequence table in an unsharded keyspace and update the VSchema to use it.

```sql theme={null}
CREATE TABLE orders_seq (
  id bigint,
  next_id bigint,
  cache bigint,
  PRIMARY KEY (id)
) COMMENT 'vitess_sequence';
```

For views in sharded keyspaces, remove the view from the sharded keyspace. Move the logic into application queries, materialize the data into a table, or use an unsharded keyspace when appropriate.

## Related docs

* [Sequence tables](../../sharding/sequence-tables.md)
* [VSchema](../../sharding/vschema.md)
* [Vindexes](../../sharding/vindexes.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
