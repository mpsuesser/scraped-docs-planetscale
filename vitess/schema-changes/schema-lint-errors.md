---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors
title: "Schema Lint Errors"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Schema lint errors

> Schema lint errors explain why a PlanetScale Vitess schema change, deploy request, or import cannot be safely applied.

**Platform availability:** Vitess only

## Overview

PlanetScale validates MySQL schema, Vitess VSchema, and routing rule changes before applying them to a production branch. These checks protect deploy requests and imports from changes that Vitess cannot apply safely or that would make data inaccessible after a merge.

When a check fails, PlanetScale returns a schema lint error with an error code, a description, and context such as the keyspace, table, column, VSchema JSON path, or vindex name. Use the error-specific pages below with the exact error description shown in the dashboard or API response.

## Error reference

* [`UNSPECIFIED`](schema-lint-errors/unspecified.md)
* [`NO_UNIQUE_KEY`](schema-lint-errors/no-unique-key.md)
* [`FOREIGN_KEYS_DISALLOWED`](schema-lint-errors/foreign-keys-disallowed.md)
* [`INVALID_CHARSET`](schema-lint-errors/invalid-charset.md)
* [`UNSUPPORTED_FEATURE`](schema-lint-errors/unsupported-feature.md)
* [`UNSUPPORTED_SHARDED_FEATURE`](schema-lint-errors/unsupported-sharded-feature.md)
* [`INVALID_VSCHEMA`](schema-lint-errors/invalid-vschema.md)
* [`DUPLICATE_ENUM_VALUE`](schema-lint-errors/duplicate-enum-value.md)
* [`UNSUPPORTED_PARTITIONING_FEATURE`](schema-lint-errors/unsupported-partitioning-feature.md)
* [`INVALID_TABLE_REFERENCED_IN_VIEW`](schema-lint-errors/invalid-table-referenced-in-view.md)
* [`INVALID_COLUMN_REFERENCED_IN_VIEW`](schema-lint-errors/invalid-column-referenced-in-view.md)
* [`BLOB_IN_UNIQUE_KEY`](schema-lint-errors/blob-in-unique-key.md)
* [`PARTIAL_KEY_IN_UNIQUE_KEY`](schema-lint-errors/partial-key-in-unique-key.md)
* [`FOREIGN_KEYS_CIRCULAR_DEPENDENCY`](schema-lint-errors/foreign-keys-circular-dependency.md)
* [`FOREIGN_KEYS_NONEXISTENT_TABLE`](schema-lint-errors/foreign-keys-nonexistent-table.md)
* [`FOREIGN_KEYS_REFERENCED_VIEW`](schema-lint-errors/foreign-keys-referenced-view.md)
* [`FOREIGN_KEYS_UNRESOLVED`](schema-lint-errors/foreign-keys-unresolved.md)
* [`INVALID_ROUTING_RULES`](schema-lint-errors/invalid-routing-rules.md)

## General troubleshooting flow

1. Read the error description first. It usually names the specific table, column, vindex, JSON path, or routing rule that failed validation.
2. Fix the schema or VSchema on a development branch.
3. If the fix depends on another object, such as a referenced table or a sequence table, deploy that dependency first.
4. Create a new deploy request after the branch schema is valid.

For broader context, see [deploy requests](deploy-requests.md), [safe migrations](safe-migrations.md), [VSchema](../sharding/vschema.md), and [Vindexes](../sharding/vindexes.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
