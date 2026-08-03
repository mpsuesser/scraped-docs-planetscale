---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/invalid-vschema
title: "Invalid Vschema"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# INVALID_VSCHEMA schema lint error

> Fix VSchema JSON and semantic validation errors in PlanetScale Vitess.

**Platform availability:** Vitess only

## What this error means

`INVALID_VSCHEMA` means the Vitess VSchema is invalid JSON, does not match the expected VSchema shape, or is inconsistent with the SQL schema.

The error usually points to the affected VSchema path, table, or vindex. Use that context to find the exact VSchema entry that failed validation.

Common messages include:

```text theme={null}
vindex not found
column "customer_id" not found
sequence table in vschema needs to be marked as sequence type
wrong type of vindex defined in column `customer_id`, expected `hash` or `xxhash`, got `unicode_loose_md5`
table orders_lookup has a changed column vindex. This will lead to inaccessible data
```

## Why PlanetScale rejects it

Vitess uses VSchema to route queries, map rows to shards, define vindexes, configure lookup vindexes, and manage sequences. If VSchema is inconsistent with the SQL schema, Vitess may route queries incorrectly or make data inaccessible.

## How to fix it

Use the highlighted VSchema path or the table and vindex named in the message to find the failing VSchema entry. Then check these common causes:

* The table named in VSchema also exists in the SQL schema.
* Every `column_vindexes` entry references an existing vindex.
* Every column used by a vindex exists in the SQL table.
* The vindex type matches the column type. Numeric columns should use `hash` or `xxhash`; text columns should use `unicode_loose_md5` or `unicode_loose_xxhash`; binary columns should use `binary_md5`.
* Sequence tables exist in SQL, are declared in VSchema as `type: "sequence"`, and contain integer `id`, `next_id`, and `cache` columns.
* Existing primary column vindexes are not changed in a way that would make existing rows inaccessible.

Example column vindex definition:

```json theme={null}
{
  "sharded": true,
  "vindexes": {
    "xxhash": { "type": "xxhash" }
  },
  "tables": {
    "orders": {
      "column_vindexes": [
        { "column": "customer_id", "name": "xxhash" }
      ]
    }
  }
}
```

## Related docs

* [VSchema](../../sharding/vschema.md)
* [Vindexes](../../sharding/vindexes.md)
* [Sequence tables](../../sharding/sequence-tables.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
