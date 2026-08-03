---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/unsupported-partitioning-feature
title: "Unsupported Partitioning Feature"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# UNSUPPORTED_PARTITIONING_FEATURE schema lint error

> Fix unsupported MySQL table partitioning features in PlanetScale Vitess.

**Platform availability:** Vitess only

## What this error means

`UNSUPPORTED_PARTITIONING_FEATURE` means a table uses a MySQL partitioning feature that PlanetScale does not support in schema linting.

Common messages include:

```text theme={null}
only RANGE, HASH, and KEY based partitioning is supported
SUBPARTITION is not supported
partition "p0" has unsupported storage engine MyISAM
SUBPARTITION for partition "p0" is not supported
```

## Why PlanetScale rejects it

Vitess already provides horizontal sharding at the keyspace level. Some MySQL table partitioning features are difficult to apply safely through online schema changes or are not compatible with the schema deployment path.

## How to fix it

Use only supported partitioning types: `RANGE`, `HASH`, or `KEY`. Remove `LIST` partitioning, subpartitioning, and non-InnoDB partition engines.

Invalid:

```sql theme={null}
CREATE TABLE events (
  id bigint unsigned NOT NULL,
  region_id int NOT NULL,
  PRIMARY KEY (id)
)
PARTITION BY LIST(region_id) (
  PARTITION us VALUES IN (1, 2),
  PARTITION eu VALUES IN (3, 4)
);
```

A supported partitioning type:

```sql theme={null}
CREATE TABLE events (
  id bigint unsigned NOT NULL,
  created_at date NOT NULL,
  PRIMARY KEY (id, created_at)
)
PARTITION BY RANGE COLUMNS(created_at) (
  PARTITION p2026 VALUES LESS THAN ('2027-01-01')
);
```

If you are using partitioning to distribute data at scale, consider Vitess sharding instead of MySQL table partitioning.

## Related docs

* [Sharding](../../sharding.md)
* [Vindexes](../../sharding/vindexes.md)
* [MySQL compatibility](../../mysql-compatibility.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
