---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/duplicate-enum-value
title: "Duplicate Enum Value"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# DUPLICATE_ENUM_VALUE schema lint error

> Fix MySQL enum definitions that repeat the same enum value.

**Platform availability:** Vitess only

## What this error means

`DUPLICATE_ENUM_VALUE` means an `ENUM` column definition contains the same value more than once. This can include duplicate empty-string values.

A common error message is:

```text theme={null}
enum value '' occurs more than once.
```

## Why PlanetScale rejects it

Enum values are stored internally by ordinal position. Duplicate values make the schema ambiguous and can cause unexpected behavior when comparing schemas, generating diffs, or applying deploy requests.

## How to fix it

Remove the duplicate enum value from the column definition.

Invalid:

```sql theme={null}
CREATE TABLE shirts (
  id bigint unsigned NOT NULL,
  size enum('small', 'medium', 'medium', 'large') NOT NULL,
  PRIMARY KEY (id)
);
```

Valid:

```sql theme={null}
CREATE TABLE shirts (
  id bigint unsigned NOT NULL,
  size enum('small', 'medium', 'large') NOT NULL,
  PRIMARY KEY (id)
);
```

If the duplicate values were meant to represent different labels, store distinct enum values and map display names in your application.

## Related docs

* [How to make different types of schema changes](../how-to-make-different-types-of-schema-changes.md)
* [Deploy requests](../deploy-requests.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
