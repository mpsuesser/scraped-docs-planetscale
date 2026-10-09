---
url: https://planetscale.com/docs/vitess/schema-changes/constraint-not-found
title: "Constraint Not Found"
description: ""
access_date: 2026-10-09T23:32:13.485Z
current_date: 2026-10-09T23:32:13.485Z
---

A deploy request can fail before copying starts because it drops a constraint by a name that does not exist on the production table:

```text
Found DROP CONSTRAINT: DROP CHECK \`orders_amount_chk\`, but could not find constraint name in map
```

MySQL requires `CHECK` and `FOREIGN KEY` constraint names to be unique across the whole schema. Because an online schema change builds a copy of the table alongside the original, PlanetScale gives every constraint on the copy a new name: the original name plus a generated suffix such as `orders_amount_chk_5vtaqz7kepok6wa91vryrkrje`. The suffix changes every time the table goes through a deploy. See [Foreign key constraint names change on every deployment](../foreign-key-constraints.md#foreign-key-constraint-names-change-on-every-deployment).

When the deploy runs, the `DROP` must name the constraint exactly as it exists on the production table at that moment. If the table was deployed again after this deploy request’s changes were computed, or the name recorded for the production branch is out of date, the names no longer match and the deploy stops.

## How to fix it

Check the current name on the production branch:

```sql
SHOW CREATE TABLE orders\G
```

Then open a new deploy request:

If the new deploy request fails with the same error, contact support with the deploy request number.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
