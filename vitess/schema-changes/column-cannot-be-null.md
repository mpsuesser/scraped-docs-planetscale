---
url: https://planetscale.com/docs/vitess/schema-changes/column-cannot-be-null
title: "Column Cannot Be Null"
description: ""
access_date: 2026-10-09T23:32:13.485Z
current_date: 2026-10-09T23:32:13.485Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Column cannot be null

> Fix a deploy that fails while copying rows because a NOT NULL column still contains NULL.

**Platform availability:** Vitess only

A deploy request can fail while copying a table because existing rows do not fit the new schema.

PlanetScale copies the table in the background and inserts those rows into a new table that uses the deployed schema. If that schema makes a column `NOT NULL` and a row still has `NULL` in the column, the copy stops:

```text theme={null}
vreplication: terminal error: task error: failed inserting rows: Column 'email' cannot be null (errno 1048) (sqlstate 23000)
```

The column name in quotes is the one to fix. The rest of the message is the insert that failed.

The copy runs while your application keeps writing to the table. If the application inserts or updates a row with `NULL` in that column during the copy, the deploy fails with a slightly different message:

```text theme={null}
vreplication: terminal error: error applying event: Column 'email' cannot be null (errno 1048) (sqlstate 23000)
```

In that case the application is still writing `NULL`. Update it to always set the column before retrying.

## How to fix it

Update the rows that still have `NULL` in that column, then [retry the failed tables](deploy-requests.md#retry-a-partially-failed-deploy).

```sql theme={null}
SELECT id FROM users WHERE email IS NULL;

UPDATE users SET email = 'unknown@example.com' WHERE email IS NULL;
```

Use a value that is valid for the new column. Retrying before those rows are updated again will cause another failure.

Retry starts the migration for the failed table over again. Tables in the same deploy that already finished copying are left as they are.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
