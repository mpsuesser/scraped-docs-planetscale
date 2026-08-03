---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/foreign-keys-nonexistent-table
title: "Foreign Keys Nonexistent Table"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# FOREIGN_KEYS_NONEXISTENT_TABLE schema lint error

> Fix foreign keys that reference tables missing from the schema.

**Platform availability:** Vitess only

## What this error means

`FOREIGN_KEYS_NONEXISTENT_TABLE` means a foreign key references a table that does not exist in the resulting schema.

A common underlying message is:

```text theme={null}
table `posts` foreign key references nonexistent table `users`
```

## Why PlanetScale rejects it

MySQL foreign keys must reference an existing base table. During a deploy request, PlanetScale validates the final schema. If the parent table is missing, the child table cannot be created or preserved with that constraint.

## How to fix it

Make sure the referenced table exists before the foreign key is created.

* Fix spelling or keyspace/schema qualification mistakes.
* Create the parent table before creating the child foreign key.
* Do not drop a parent table while a child foreign key still references it.
* Split complex changes into multiple deploy requests.

Invalid:

```sql theme={null}
CREATE TABLE posts (
  id bigint unsigned NOT NULL,
  author_id bigint unsigned NOT NULL,
  PRIMARY KEY (id),
  CONSTRAINT posts_author_fk FOREIGN KEY (author_id) REFERENCES users(id)
);
```

If `users` is missing, create it first or remove the constraint until the parent exists.

## Related docs

* [Foreign key constraints](../../foreign-key-constraints.md)
* [Deploy requests](../deploy-requests.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
