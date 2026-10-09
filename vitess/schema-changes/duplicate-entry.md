---
url: https://planetscale.com/docs/vitess/schema-changes/duplicate-entry
title: "Duplicate Entry"
description: ""
access_date: 2026-10-09T23:32:13.485Z
current_date: 2026-10-09T23:32:13.485Z
---

A deploy request can fail while copying a table because existing rows do not satisfy a unique key in the new schema.

PlanetScale copies the table in the background and inserts existing rows into a new table that uses the deployed schema. If that schema adds a `UNIQUE` key, or changes the primary key, and two existing rows have the same value for those columns, the second insert is rejected:

```text
vreplication: terminal error: task error: failed inserting rows: Duplicate entry 'acme-1' for key 'uniq_org_priority' (errno 1062) (sqlstate 23000)
```

The first quoted value is one of the duplicates. The key name tells you which columns are in conflict. The key name may be shown with a `_vt_vrp_...` prefix, which is the name of the table being copied into.

## How to fix it

Find the rows that share a value for the columns in the key, then remove or change them so each combination is unique:

```sql
SELECT org_id, priority, COUNT(*)
FROM packaging_rules
GROUP BY org_id, priority
HAVING COUNT(*) > 1;
```

Then [retry the failed tables](deploy-requests.md#retry-a-partially-failed-deploy). Retrying before every duplicate is resolved will cause another failure.

If the duplicates are expected, change the key on your branch so it is no longer unique, or add a column to it that makes each row distinct, and open a new deploy request.

The copy runs while your application keeps writing to the table. If the application inserts a duplicate during the copy, the deploy fails with the same error. Make sure new writes also satisfy the unique key before retrying.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
