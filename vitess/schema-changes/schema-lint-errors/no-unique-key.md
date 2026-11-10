---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/no-unique-key
title: "No Unique Key"
description: ""
access_date: 2026-11-10T16:33:18.688Z
current_date: 2026-11-10T16:33:18.688Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# NO_UNIQUE_KEY schema lint error

> Fix tables that do not have a unique, not-null key for PlanetScale online schema changes.

This page covers Vitess.

## What this error means

`NO_UNIQUE_KEY` means a table does not have a usable unique, not-null key. A `PRIMARY KEY` satisfies this requirement. A `UNIQUE KEY` can also satisfy it if every indexed column is `NOT NULL` and the key does not use a text/blob column or a prefix index.

A common error message is:

```text theme={null}
table "orders" has no non-null unique key: all tables must have at least one unique, not-null key without using text / blob columns or partial indexes.
```

## Why PlanetScale rejects it

PlanetScale uses Vitess online schema change workflows to apply changes without downtime. Those workflows need a stable row identity while copying and reconciling data. Nullable unique keys do not qualify because MySQL allows multiple `NULL` values in a unique index.

Without a primary key or a `NOT NULL` unique key, InnoDB uses an internal hidden row ID that is assigned independently on each MySQL instance, so it is not a shared identity across PRIMARY, REPLICA, and RDONLY tablets. Online DDL (VReplication) also needs a shared unique key between the source and target schemas, so adding the first primary key to a keyless table fails with `found no possible unique key`.

## How to fix it

Define a primary key when you create the table. A `PRIMARY KEY` satisfies the unique, not-null key requirement.

```sql theme={null}
CREATE TABLE orders (
  id bigint unsigned NOT NULL,
  customer_id bigint unsigned NOT NULL,
  created_at datetime NOT NULL,
  PRIMARY KEY (id)
);
```

If your only candidate key uses a `TEXT`, `BLOB`, or prefix index, use a surrogate key such as a bigint or UUID column instead.

> [!WARNING]
>
> If the table already exists and has no primary key or unique `NOT NULL` key, do not add an `AUTO_INCREMENT` primary key with a direct `ALTER TABLE` (including with [safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) disabled):
>
> ```sql theme={null}
> ALTER TABLE orders ADD COLUMN id BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY;
> ```
>
> That change can silently diverge data between tablets and lead to data loss during failover. [Contact PlanetScale Support](https://planetscale.com/contact?initial=support) rather than adding the key yourself. See [Tables without a primary key](https://planetscale.com/docs/vitess/schema-changes/onlineddl-change-unique-keys.md#tables-without-a-primary-key).

## Related docs

* [Changing primary and unique keys](https://planetscale.com/docs/vitess/schema-changes/onlineddl-change-unique-keys.md)
* [Tables without a primary key](https://planetscale.com/docs/vitess/schema-changes/onlineddl-change-unique-keys.md#tables-without-a-primary-key)
* [How online schema change tools work](https://planetscale.com/docs/vitess/schema-changes/how-online-schema-change-tools-work.md)
* [Safe migrations](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
