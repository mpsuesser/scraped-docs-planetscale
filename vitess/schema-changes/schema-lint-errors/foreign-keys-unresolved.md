---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/foreign-keys-unresolved
title: "Foreign Keys Unresolved"
description: ""
access_date: 2026-09-04T19:51:53.762Z
current_date: 2026-09-04T19:51:53.762Z
---

## What this error means

`FOREIGN_KEYS_UNRESOLVED` means Vitess could not resolve the dependency ordering for a table’s foreign keys.

A common underlying message is:

```text
table \`posts\` has unresolved foreign key dependencies
```

## Why PlanetScale rejects it

PlanetScale needs to know the order in which tables and constraints can be applied. Foreign keys introduce dependencies between tables. If those dependencies are missing, conflicting, cyclic, or changed together in an unsafe order, Vitess cannot safely build the final schema.

## How to fix it

Check the table named in the error and each foreign key it defines.

- Confirm every referenced table exists in the final schema.
- Confirm every referenced column exists and is indexed as required by MySQL.
- Create parent tables before child constraints.
- Drop child constraints before dropping referenced parent tables or columns.
- Split dependent changes into separate deploy requests.
- Break cycles where possible.

For example, deploy parent table changes first, then add child constraints in a later deploy request.

## Related docs

- [Foreign key constraints](../../foreign-key-constraints.md)
- [Deploy requests](../deploy-requests.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
