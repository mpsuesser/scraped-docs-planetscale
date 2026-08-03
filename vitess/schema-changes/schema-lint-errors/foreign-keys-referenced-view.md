---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/foreign-keys-referenced-view
title: "Foreign Keys Referenced View"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# FOREIGN_KEYS_REFERENCED_VIEW schema lint error

> Fix foreign keys that reference views instead of base tables.

**Platform availability:** Vitess only

## What this error means

`FOREIGN_KEYS_REFERENCED_VIEW` means a foreign key references a view.

A common underlying message is:

```text theme={null}
table `posts` foreign key references view `active_users`
```

## Why PlanetScale rejects it

MySQL foreign key constraints must reference real base tables. A view is a stored query, not a table that can enforce referential integrity.

## How to fix it

Reference the underlying table instead of the view, or remove the foreign key constraint and enforce the relationship in application code.

Invalid:

```sql theme={null}
CREATE VIEW active_users AS
SELECT id FROM users WHERE active = 1;

CREATE TABLE posts (
  id bigint unsigned NOT NULL,
  author_id bigint unsigned NOT NULL,
  PRIMARY KEY (id),
  CONSTRAINT posts_author_fk FOREIGN KEY (author_id) REFERENCES active_users(id)
);
```

Valid table reference:

```sql theme={null}
CREATE TABLE posts (
  id bigint unsigned NOT NULL,
  author_id bigint unsigned NOT NULL,
  PRIMARY KEY (id),
  CONSTRAINT posts_author_fk FOREIGN KEY (author_id) REFERENCES users(id)
);
```

## Related docs

* [Foreign key constraints](../../foreign-key-constraints.md)
* [Operating without foreign key constraints](../../operating-without-foreign-key-constraints.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
