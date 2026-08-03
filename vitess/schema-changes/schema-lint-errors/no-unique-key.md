---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/no-unique-key
title: "No Unique Key"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# NO_UNIQUE_KEY schema lint error

> Fix tables that do not have a unique, not-null key for PlanetScale online schema changes.

**Platform availability:** Vitess only

## What this error means

`NO_UNIQUE_KEY` means a table does not have a usable unique, not-null key. A `PRIMARY KEY` satisfies this requirement. A `UNIQUE KEY` can also satisfy it if every indexed column is `NOT NULL` and the key does not use a text/blob column or a prefix index.

A common error message is:

```text theme={null}
table "orders" has no non-null unique key: all tables must have at least one unique, not-null key without using text / blob columns or partial indexes.
```

## Why PlanetScale rejects it

PlanetScale uses Vitess online schema change workflows to apply changes without downtime. Those workflows need a stable row identity while copying and reconciling data. Nullable unique keys do not qualify because MySQL allows multiple `NULL` values in a unique index.

## How to fix it

Add a primary key or a unique key over columns that are all declared `NOT NULL`.

```sql theme={null}
CREATE TABLE orders (
  id bigint unsigned NOT NULL,
  customer_id bigint unsigned NOT NULL,
  created_at datetime NOT NULL,
  PRIMARY KEY (id)
);
```

If the table already exists and the intended key column contains `NULL` values, fix the data first, then make the column `NOT NULL`, then add the unique key. For large tables, split those steps into separate deploy requests.

```sql theme={null}
ALTER TABLE orders MODIFY id bigint unsigned NOT NULL;
ALTER TABLE orders ADD UNIQUE KEY orders_id_idx (id);
```

If your only candidate key uses a `TEXT`, `BLOB`, or prefix index, use a surrogate key such as a bigint or UUID column instead.

## Related docs

* [Changing primary and unique keys](../onlineddl-change-unique-keys.md)
* [How online schema change tools work](../how-online-schema-change-tools-work.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
