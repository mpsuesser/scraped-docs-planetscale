---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/partial-key-in-unique-key
title: "Partial Key In Unique Key"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PARTIAL_KEY_IN_UNIQUE_KEY schema lint error

> Fix primary keys that use prefix indexes.

**Platform availability:** Vitess only

## What this error means

`PARTIAL_KEY_IN_UNIQUE_KEY` means a primary key uses a prefix length, such as `name(10)`.

A common error message is:

```text theme={null}
table "customers" contains partial index on column "email" in the primary key.
```

## Why PlanetScale rejects it

A prefix index only indexes part of a column value. PlanetScale needs a unique key that can identify the full row during online schema changes. A partial key is not accepted as that identity.

## How to fix it

Use a full column in the primary key, add a surrogate primary key, or add a separate full unique key over `NOT NULL` columns.

Invalid:

```sql theme={null}
CREATE TABLE customers (
  email varchar(255) NOT NULL,
  PRIMARY KEY (email(32))
);
```

Recommended:

```sql theme={null}
CREATE TABLE customers (
  id bigint unsigned NOT NULL,
  email varchar(255) NOT NULL,
  PRIMARY KEY (id),
  UNIQUE KEY customers_email_idx (email)
);
```

If the full column is too large for your indexing strategy, use a shorter bounded column type or a separate stable identifier.

## Related docs

* [Changing primary and unique keys](../onlineddl-change-unique-keys.md)
* [MySQL compatibility](../../mysql-compatibility.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
