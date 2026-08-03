---
url: https://planetscale.com/docs/vitess/schema-changes/schema-lint-errors/unspecified
title: "Unspecified"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# UNSPECIFIED schema lint error

> How to troubleshoot an unclassified PlanetScale schema lint error.

**Platform availability:** Vitess only

## What this error means

`UNSPECIFIED` is a fallback schema lint error. PlanetScale found a schema, VSchema, or routing rule problem, but the failure did not map to a more specific lint code.

The message shown with the error is the most important part of this error. It may include the table, keyspace, statement, or lower-level validation message that caused the check to fail.

## Why PlanetScale rejects it

Deploy requests and imports must produce a schema Vitess can safely apply. If the validator cannot classify the failure, PlanetScale still blocks the change instead of applying an unknown or unsafe schema state.

## How to fix it

1. Read the full error message carefully and identify the referenced keyspace, table, column, VSchema path, or routing rule.
2. Check whether another page in this error reference matches the same message.
3. Reproduce the change on a development branch and make the smallest schema change needed to clear the message.
4. If the message is not actionable, contact support with the database, branch, deploy request, and full lint error details.

## Related docs

* [Schema lint errors](../schema-lint-errors.md)
* [Deploy requests](../deploy-requests.md)
* [MySQL compatibility](../../mysql-compatibility.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
