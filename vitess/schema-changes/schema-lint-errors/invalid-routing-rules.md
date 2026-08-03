---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/invalid-routing-rules
title: "Invalid Routing Rules"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# INVALID_ROUTING_RULES schema lint error

> Fix invalid Vitess routing rules in PlanetScale.

**Platform availability:** Vitess only

## What this error means

`INVALID_ROUTING_RULES` means the Vitess routing rules for the schema are invalid or conflict with the target branch during a merge.

Common messages include:

```text theme={null}
table not found
keyspace `target` not found in schema
table "t" must be qualified
table `source.t` has no targets
table `source.t` has more than one target
table "t@rdonly" already routes to different table in destination routing rules and conflicts
```

## Why PlanetScale rejects it

Routing rules tell Vitess how to route queries from one table name to another table. Invalid rules can send traffic to a missing table, an ambiguous keyspace, multiple targets, or a conflicting destination rule.

## How to fix it

Use the routing rule location and the full error message to find the failing rule.

* Qualify table names as `<keyspace>.<table>`.
* Verify the target keyspace exists.
* Verify the target table exists in the SQL schema.
* Use exactly one target table for each rule.
* Remove duplicate source table entries.
* Use only valid tablet-type suffixes, such as `@primary`, `@replica`, or `@rdonly`.
* If the error is a merge conflict, reconcile the destination branch's routing rules first, then recreate the deploy request.

Example shape:

```json theme={null}
{
  "rules": [
    {
      "from_table": "source.orders",
      "to_tables": ["target.orders"]
    }
  ]
}
```

## Related docs

* [VSchema](../../sharding/vschema.md)
* [Sharding quickstart](../../sharding/sharding-quickstart.md)
* [Sharding workflow state reference](../../sharding/sharding-workflow-state-reference.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
