---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/invalid-column-referenced-in-view
title: "Invalid Column Referenced In View"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# INVALID_COLUMN_REFERENCED_IN_VIEW schema lint error

> Fix views that reference missing, ambiguous, or unsafe columns.

**Platform availability:** Vitess only

## What this error means

`INVALID_COLUMN_REFERENCED_IN_VIEW` means a view references a column that Vitess cannot resolve in the final schema. This can also happen when a view uses a star expression that cannot be safely expanded.

Common underlying messages include:

```text theme={null}
view `active_users` references unqualified but non-existent column `email`
view `active_users` references unqualified but non unique column `id`
view `active_users` has invalid star expression
```

## Why PlanetScale rejects it

A view must remain valid after the deploy request is applied. If a column is dropped, renamed, ambiguous across joined tables, or hidden behind `SELECT *`, Vitess cannot reliably generate a safe schema diff.

## How to fix it

Use explicit column lists and update the view before dropping or renaming columns.

Avoid this:

```sql theme={null}
CREATE VIEW active_users AS
SELECT * FROM users WHERE active = 1;
```

Prefer this:

```sql theme={null}
CREATE VIEW active_users AS
SELECT id, email, created_at
FROM users
WHERE active = 1;
```

If you need to rename a column used by a view, split the migration:

1. Add the new column and update application writes.
2. Update the view to reference the new column.
3. Drop the old column after the view and application no longer use it.

## Related docs

* [Handling table and column renames](../handling-table-and-column-renames.md)
* [Deploy requests](../deploy-requests.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
