---
url: https://planetscale.com/docs/vitess/schema-changes/how-to-make-different-types-of-schema-changes
title: "How To Make Different Types Of Schema Changes"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Overview

So schema changes are inevitable. Luckily, PlanetScale has features like [branching](branching.md), [deploy requests](deploy-requests.md), and [revert](deploy-requests.md#revert-a-schema-change) to help give you more confidence when making schema changes. But you still have to consider though how to make the schema change. Some schema changes are more complex than others. This document will help you understand how to make different types of schema changes and associated risks.

Most of the following applies to any relational database and not only to PlanetScale.

## Adding a table

Adding a table is a low risk since it does not affect the existing schema. Once you add the table to your schema and deploy the database to production, if you want to backfill the data, now would be the time. Otherwise, you add it to your application code after completing your database deployment. Deploying the database first and then changing your application code to avoid downtime is best.

## Adding a column

Adding a column without a `NOT NULL` constraint is a low risk and can be handled similarly to [adding a table](#adding-a-table).

The one exception is if you want to add a column with a `NOT NULL` constraint and/or no `DEFAULT` value. The risk increases because the previously inserted rows will be expected to be `NOT NULL` or have no default values; this will cause possible errors in your application.

To work around this, you can do the following:

## Changing a column or table

Some examples of changing a column or table include:

- Renaming an existing column or table
- Changing the data type of an existing column
- Splitting and other modifications to the data of an existing column or table

This includes modifying your application to use a different column or table in existing code. For example, if you are using `username` in the code but instead want to start using a new `user_id` column in the existing code.

Changing a column or table your current application uses is a high risk. It can cause disruptions to users and possible downtime. If you want to change a column or table, we recommend following the expand, migrate, and contract pattern described in the [backward compatible schema changes](https://planetscale.com/blog/backward-compatible-databases-changes) blog post. The blog post walks you through each step in both your database and application code and gives a detailed example.

## Removing a column or table

### In use

Removing a column or table your current application uses is a high risk. Dropping a column or table can cause disruptions to users and possible downtime. If you want to remove a column or table in use, we recommend following the expand, migrate, and contract pattern described in the [backward compatible schema changes](https://planetscale.com/blog/backward-compatible-databases-changes) blog post. The blog post walks you through each step in both your database and application code and gives a detailed example.

### Not in use

Removing a column or table that is no longer in use by your current application is a low risk. The only danger is potentially needing the removed data in the future.

## Other schema changes

This is not an exhaustive list of schema changes. Remember, making smaller incremental schema changes is always safer than larger, more complex ones. If you want to read more on making safer schema changes with PlanetScale, read this blog post on [safely making database schema changes](https://planetscale.com/blog/safely-making-database-schema-changes).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
