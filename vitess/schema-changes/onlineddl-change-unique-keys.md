---
url: https://planetscale.com/docs/vitess/schema-changes/onlineddl-change-unique-keys
title: "Onlineddl Change Unique Keys"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Online DDL change unique keys

> It is possible to modify or replace a table's `PRIMARY KEY`, or any other `UNIQUE KEY`s according to the limitation described below, followed by examples.

**Platform availability:** Vitess only

## Overview

To migrate data safely and [without downtime](../schema-changes.md), PlanetScale requires that all tables have a unique, not-null key. Note that a `PRIMARY KEY` satisfies this condition, and it is generally recommended to always have a `PRIMARY KEY` on all tables.

When you modify a table, both the old and the new schema must have a unique key as described, and the columns covered by those keys must exist in both the old and the new schema.

Essentially this makes it possible for PlanetScale to unambiguously identify and correlate a row between the two schemas.

If you attempt to deploy a schema change which does not comply with the above restriction, the deploy request will fail with the error `Table ... has no shared columns covered by non-null unique keys between both branches.`.

## Examples: allowed changes

In our examples, we assume the base schema to be:

```sql theme={null}
CREATE TABLE `users` (
	`id` int,
	`other_info` int,
	`username` varchar(128),
	`email` varchar(128),
	PRIMARY KEY (`id`)
);
```

The following are all valid changes to the schema:

### Expanding the PRIMARY KEY

```sql theme={null}
CREATE TABLE `users` (
	`id` int,
	`other_info` int,
	`username` varchar(128),
	`email` varchar(128),
	PRIMARY KEY (`id`, `other_info`)
);
```

In the above we modified the `PRIMARY KEY` to include `other_info`. This is allowed since both `id` and `other_info` columns exist in both the old and the new schema.

### Moving PRIMARY KEY to a different column

```sql theme={null}
CREATE TABLE `users` (
	`id` int,
	`other_info` int,
	`username` varchar(128),
	`email` varchar(128),
	PRIMARY KEY (`email`)
);
```

Since both `id` and `email` columns exist in both old and new schema, the deploy request will be allowed. The success of the operation depends on whether `email` actually contains unique values. If there's duplication in `email` values, the deployment will fail with error.

### Moving PRIMARY KEY to different columns

Likewise, there is no problem if the new `PRIMARY KEY` covers multiple columns. Again, the success of the operation depends on the actual uniqueness of the combination of columns.

```sql theme={null}
CREATE TABLE `users` (
	`id` int,
	`other_info` int,
	`username` varchar(128),
	`email` varchar(128),
	PRIMARY KEY (`username`, `other_info`)
);
```

### Changing PRIMARY KEY and adding/removing other UNIQUE KEYs

```sql theme={null}
CREATE TABLE `users` (
	`id` int,
	`other_info` int,
	`username` varchar(128),
	`email` varchar(128),
	PRIMARY KEY (`username`, `other_info`),
	UNIQUE KEY `email` (`email`)
);
```

## Examples: invalid changes

Consider the next scenarios and the ways to work around them:

### Changing a PRIMARY KEY to include a new column

```sql theme={null}
CREATE TABLE `users` (
	`id` int,
	`other_info` int,
	`username` varchar(128),
	`email` varchar(128),
	`new_info` int,
	PRIMARY KEY (`username`, `new_info`)
);
```

This is an invalid change because in the new schema, the `PRIMARY KEY` covers the `new_info` column. But this column does not exist in the old schema.

Consider splitting into two distinct schema changes and deploy requests:

1. First, introduce the `new_info` column.
2. Next, change the `PRIMARY KEY`.

### Changing a PRIMARY KEY and also dropping the old covered column

```sql theme={null}
CREATE TABLE `users` (
	`other_info` int,
	`username` varchar(128),
	`email` varchar(128),
	PRIMARY KEY (`email`)
);
```

The above is invalid because `id` column, covered by the `PRIMARY KEY` in the old schema, does not exist in the new schema.

Again, consider splitting into two distinct changes:

1. First, change the `PRIMARY KEY`.
2. Next, drop the `id` column.

## Summary

We've seen how, in many scenarios, it's straightforward to modify your table's `PRIMARY KEY` or other keys. For some scenarios, it might take two or more steps to achieve the new schema.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
