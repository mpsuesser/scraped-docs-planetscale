---
url: https://planetscale.com/docs/vitess/schema-changes/column-has-no-default
title: "Column Has No Default"
description: ""
access_date: 2026-10-09T23:32:13.485Z
current_date: 2026-10-09T23:32:13.485Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Column has no default value

> Fix a deploy that fails while copying rows because a new NOT NULL column has no DEFAULT.

**Platform availability:** Vitess only

A deploy request can fail while copying a table because a column added by the deploy has no value for the existing rows.

PlanetScale copies the table in the background and inserts existing rows into a new table that uses the deployed schema. Only the columns that exist in both tables are copied. If the new table adds a column that is `NOT NULL` and has no `DEFAULT`, the insert has nothing to put in it and MySQL rejects the row:

```text theme={null}
vreplication: terminal error: task error: failed inserting rows: Field 'updatedAt' doesn't have a default value (errno 1364) (sqlstate HY000)
```

The column name in quotes is the one to fix.

In plain MySQL, `ALTER TABLE ... ADD COLUMN` fills existing rows with an implicit default such as `0` or `''`. The copy uses `INSERT` statements under strict SQL mode instead, so there is no implicit default to fall back on.

This also happens when a column is renamed. PlanetScale treats a rename as dropping the old column and adding a new one, so the new column has no values to copy. See [Handling table and column renames](handling-table-and-column-renames.md).

## How to fix it

Change the column definition on your branch so existing rows have a value to copy:

```sql theme={null}
-- Give the column a default
ALTER TABLE users ADD COLUMN updatedAt datetime NOT NULL DEFAULT CURRENT_TIMESTAMP;

-- Or allow NULL for now and tighten it later
ALTER TABLE users ADD COLUMN updatedAt datetime NULL;
```

Then open a new deploy request. If the failed deploy is still in progress, cancel it first. Changing the schema on your branch does not update a deploy that has already started.

If you allowed `NULL`, backfill the column after the deploy, then make it `NOT NULL` in a later deploy request.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
