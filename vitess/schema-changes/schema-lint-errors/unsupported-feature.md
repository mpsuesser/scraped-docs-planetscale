---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/unsupported-feature
title: "Unsupported Feature"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# UNSUPPORTED_FEATURE schema lint error

> Resolve PlanetScale schema lint errors for unsupported MySQL or Vitess features.

**Platform availability:** Vitess only

## What this error means

`UNSUPPORTED_FEATURE` is used for schema features PlanetScale cannot safely apply through Vitess. The exact fix depends on the full error message and any context shown with it, such as the affected table, storage engine, or check constraint.

Known cases include:

* Non-InnoDB storage engines, such as `MyISAM`.
* Unsupported SQL features such as `JSON_TABLE`.
* Too many columns in one table.
* Too many tables or views in one schema.
* Check constraint names that are too long for older MySQL/Vitess rename behavior.
* Generic precondition failures that do not have a more specific lint code.

## Why PlanetScale rejects it

Deploy requests are applied by Vitess. Some MySQL features either are not supported by Vitess, cannot be represented safely in the online schema change flow, or exceed limits that protect branch creation and deploy reliability.

## How to fix it

Start with the exact error description.

If the error names a storage engine, use InnoDB:

```sql theme={null}
ALTER TABLE archived_orders ENGINE = InnoDB;
```

If the error mentions too many columns or too many tables/views, split the schema or reduce the object count before deploying.

If the error mentions a check constraint name, shorten the constraint name. For older MySQL versions, avoid long generated check names and avoid names with more than three characters after a `<table>_chk_` prefix.

```sql theme={null}
ALTER TABLE accounts
  ADD CONSTRAINT balance_chk CHECK (balance >= 0);
```

If the error mentions `JSON_TABLE`, rewrite the query or schema design to avoid relying on `JSON_TABLE` in PlanetScale Vitess.

## Related docs

* [MySQL compatibility](../../mysql-compatibility.md)
* [PlanetScale system limits](../../troubleshooting/planetscale-system-limits.md)
* [Deploy requests](../deploy-requests.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
