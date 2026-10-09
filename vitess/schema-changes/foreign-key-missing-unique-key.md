---
url: https://planetscale.com/docs/vitess/schema-changes/foreign-key-missing-unique-key
title: "Foreign Key Missing Unique Key"
description: ""
access_date: 2026-10-09T23:32:13.485Z
current_date: 2026-10-09T23:32:13.485Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Foreign key missing unique key

> A deploy fails because a foreign key references columns that are not covered by a unique key.

**Platform availability:** Vitess only

A deploy request can fail before copying starts because a [foreign key constraint](../foreign-key-constraints.md) references columns on the parent table that are not covered by a unique key:

```text theme={null}
Failed to add the foreign key constraint. Missing unique key for constraint 'fk_assessments_group' in the referenced table 'student_groups' (errno 6125) (sqlstate HY000)
```

MySQL 8.0 allowed a foreign key to reference any indexed column, even when the index was not unique or the foreign key covered only a prefix of it. MySQL 8.4 rejects both by default. The referenced columns must be the `PRIMARY KEY` or a `UNIQUE` key on exactly those columns.

## How to fix it

Add a unique key on the referenced columns of the parent table, then deploy again:

```sql theme={null}
ALTER TABLE student_groups ADD UNIQUE KEY uniq_student_groups_id (id);
```

If the referenced column is not unique by design, point the foreign key at the parent table's primary key instead, or drop the constraint and enforce the relationship in your application. See [Strategies for maintaining referential integrity](../strategies-for-maintaining-referential-integrity.md).

Make the change on your branch, then open a new deploy request. If the failed deploy is still in progress, cancel it first.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
