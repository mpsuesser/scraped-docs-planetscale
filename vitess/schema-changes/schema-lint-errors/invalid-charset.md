---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/invalid-charset
title: "Invalid Charset"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# INVALID_CHARSET schema lint error

> Fix unsupported table or column character sets in a PlanetScale Vitess schema.

**Platform availability:** Vitess only

## What this error means

`INVALID_CHARSET` means a table or column uses a character set that PlanetScale does not allow for schema linting.

Common messages include:

```text theme={null}
table "users" has an invalid table charset utf16: only utf8, utf8mb4, and utf8mb3 charsets are supported.
table "users" has an invalid column charset utf16: only utf8, utf8mb4, and utf8mb3 charsets are supported.
```

PlanetScale supports `utf8mb4`, `utf8mb3`, `utf8`, `latin1`, and `ascii`. We recommend `utf8mb4` for new schemas.

## Why PlanetScale rejects it

Vitess must be able to parse, compare, and apply schemas consistently across branches and tablets. Unsupported charsets can make schema diffs and data movement unsafe or incompatible with the target MySQL configuration.

## How to fix it

Convert the table or column to a supported character set, preferably `utf8mb4`.

```sql theme={null}
ALTER TABLE users CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci;
```

For a single column override, update the column definition:

```sql theme={null}
ALTER TABLE users
  MODIFY display_name varchar(255)
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_0900_ai_ci
  NOT NULL;
```

If the table contains existing data, test the conversion on a development branch first and confirm your application handles the new collation rules.

## Related docs

* [MySQL compatibility](../../mysql-compatibility.md)
* [Deploy requests](../deploy-requests.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
