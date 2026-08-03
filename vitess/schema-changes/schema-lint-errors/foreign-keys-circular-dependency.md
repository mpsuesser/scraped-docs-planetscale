---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/foreign-keys-circular-dependency
title: "Foreign Keys Circular Dependency"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# FOREIGN_KEYS_CIRCULAR_DEPENDENCY schema lint error

> Fix circular foreign key dependencies in PlanetScale Vitess schemas.

**Platform availability:** Vitess only

## What this error means

`FOREIGN_KEYS_CIRCULAR_DEPENDENCY` means foreign key relationships form a cycle. This can be a self-referencing table or a group of tables that reference each other in a loop.

## Why PlanetScale rejects it

Foreign key cycles can make schema dependency ordering ambiguous. They can also produce unsupported cascaded behavior, especially when all participating constraints use `CASCADE` or `SET NULL` actions.

## How to fix it

Break the cycle where possible:

* Remove one database-enforced foreign key constraint and enforce that relationship in application code.
* Replace cascading actions with explicit application workflows.
* Split schema changes so parent tables and child constraints are introduced in a predictable order.
* For self-references, check whether the constraint is necessary or whether application validation is enough.

Example cycle:

```sql theme={null}
CREATE TABLE teams (
  id bigint unsigned NOT NULL,
  owner_user_id bigint unsigned NOT NULL,
  PRIMARY KEY (id),
  CONSTRAINT teams_owner_fk FOREIGN KEY (owner_user_id) REFERENCES users(id)
);

CREATE TABLE users (
  id bigint unsigned NOT NULL,
  team_id bigint unsigned NOT NULL,
  PRIMARY KEY (id),
  CONSTRAINT users_team_fk FOREIGN KEY (team_id) REFERENCES teams(id)
);
```

One common fix is to remove one constraint and keep an index for the relationship:

```sql theme={null}
CREATE TABLE users (
  id bigint unsigned NOT NULL,
  team_id bigint unsigned NOT NULL,
  PRIMARY KEY (id),
  KEY users_team_idx (team_id)
);
```

## Related docs

* [Foreign key constraints](../../foreign-key-constraints.md#cyclic-foreign-keys)
* [Operating without foreign key constraints](../../operating-without-foreign-key-constraints.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
