---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/invalid-table-referenced-in-view
title: "Invalid Table Referenced In View"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# INVALID_TABLE_REFERENCED_IN_VIEW schema lint error

> Fix views that reference missing tables or unresolved view dependencies.

**Platform availability:** Vitess only

## What this error means

`INVALID_TABLE_REFERENCED_IN_VIEW` means a view references a table or entity that Vitess cannot resolve in the resulting schema. The error usually includes the view name and the missing referenced table.

## Why PlanetScale rejects it

PlanetScale compares the final schema state when building a deploy request. A view must be valid in that final schema. If the referenced table is missing, renamed, dropped, or involved in an unresolved dependency loop, Vitess cannot safely create or preserve the view.

## How to fix it

Check the view definition and make sure every referenced table exists after the deploy request is applied.

Invalid:

```sql theme={null}
CREATE VIEW active_users AS
SELECT id, email FROM users_old WHERE active = 1;
```

If `users_old` no longer exists, update the view:

```sql theme={null}
CREATE VIEW active_users AS
SELECT id, email FROM users WHERE active = 1;
```

When renaming or replacing a table used by a view, split the work into multiple deploy requests:

1. Create the replacement table or compatibility view.
2. Update dependent views to reference the new object.
3. Drop the old table after no views depend on it.

## Related docs

* [Handling table and column renames](../handling-table-and-column-renames.md)
* [Deploy requests](../deploy-requests.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
