---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/foreign-keys-disallowed
title: "Foreign Keys Disallowed"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# FOREIGN_KEYS_DISALLOWED schema lint error

> Fix schemas that define foreign key constraints when foreign key support is disabled.

**Platform availability:** Vitess only

## What this error means

`FOREIGN_KEYS_DISALLOWED` means the schema contains a `FOREIGN KEY` constraint, but the database is not configured to allow foreign key constraints.

A common error message is:

```text theme={null}
table "posts" has a foreign key constraint: foreign key constraints are not supported.
```

The error may also include the local column names used by the foreign key.

## Why PlanetScale rejects it

PlanetScale databases can run with foreign key constraints disabled or managed. When constraints are disabled, Vitess rejects DDL that creates them so the database does not enter a mixed or unsupported foreign key mode.

## How to fix it

Choose one of these approaches:

* Enable foreign key constraint support for the database if your workload needs database-enforced referential integrity and the database meets the prerequisites.
* Remove the `CONSTRAINT ... FOREIGN KEY` clause and keep ordinary indexes for query performance.
* Enforce referential integrity in application code or background jobs instead of with database constraints.

Invalid when foreign keys are disabled:

```sql theme={null}
CREATE TABLE posts (
  id bigint unsigned NOT NULL,
  author_id bigint unsigned NOT NULL,
  PRIMARY KEY (id),
  CONSTRAINT posts_author_fk FOREIGN KEY (author_id) REFERENCES users(id)
);
```

Constraint-free version:

```sql theme={null}
CREATE TABLE posts (
  id bigint unsigned NOT NULL,
  author_id bigint unsigned NOT NULL,
  PRIMARY KEY (id),
  KEY posts_author_idx (author_id)
);
```

## Related docs

* [Foreign key constraints](../../foreign-key-constraints.md)
* [Operating without foreign key constraints](../../operating-without-foreign-key-constraints.md)
* [Strategies for maintaining referential integrity](../../strategies-for-maintaining-referential-integrity.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
