---
url: https://planetscale.com/docs/vitess/schema-changes
title: "Schema Changes"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Overview

To make non-blocking schema changes in PlanetScale, you’ll first need a basic understanding of [branching](schema-changes/branching.md), the core PlanetScale feature that provides schema changes. Our branching page is a great place to start.

PlanetScale makes it safe to deploy schema changes to production databases via *development* and *production branches with [safe migrations](schema-changes/safe-migrations.md) enabled*. Branches with safe migrations enabled can only be updated using deploy requests, and default branches cannot be deleted. Development branches are a separate database with a copy of the source branch’s schema. Developers can make schema changes in development branches, test locally, and open a deploy request for deploying their changes to the production database.

Developers can also comment on deploy requests and request reviewers to approve a deploy request before its schema changes can deploy into base branches. Currently, requiring approval is a per-database setting that is turned off by default. With the setting turned off, developers do not need approval to merge a deploy request.

## Adding columns to large tables with PlanetScale is safe!

*Create*, *drop*, and *alter* statements, also known as Data Definition Language (DDL), are used for making schema changes in a database table.

PlanetScale enables developers to make schema changes without the fear of dropping columns, locking tables, causing downtime in their app, etc. PlanetScale also prevents schema changes with conflicts from being migrated and handles schema changes from multiple teammates. A user doesn’t have to wait to find out if their changes will be rejected, they learn as they add the change to the queue.

## How do I make non-blocking schema changes with PlanetScale?

In order to make non-blocking schema changes, you **must** enable [safe migrations](schema-changes/safe-migrations.md) on your production branch. Without safe migrations enabled, your schema changes will run directly on your production branch, which can lead to table locking. When safe migrations are enabled on a branch, all schema changes must occur on a database branch. *(A database branch is a separate database with a copy of the production branch’s schema.)*

At a high level, this is what happens during the *non-blocking schema change* process in PlanetScale:

PlanetScale makes sure not to exhaust your resources; the deployment may be throttled to avoid any impact on production queries.

![PlanetScale non-blocking schema changes diagram](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/nonblocking-schema-changes/diagram.png?w=2500&fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=1546b5a6690ef6b2ce281919203804e3)

PlanetScale non-blocking schema changes diagram

## PlanetScale workflow

The PlanetScale command-line tool (CLI) runs an interactive shell equipped with many commands designed to make the database management workflow easier for developers.

A basic non-blocking schema change workflow in PlanetScale might look like this:

## Limitations

If you want to make schema changes containing foreign key constraints, enable foreign key constraint support for your database in the database settings page.

PlanetScale doesn’t support direct `RENAME` for columns and tables. Learn why and how to rename tables or columns in [this tutorial](schema-changes/handling-table-and-column-renames.md).

A single deploy request can alter up to 10 tables. To make more changes you’ll want to split up your migration over multiple deploy requests or temporarily disable safe-migrations and apply the changes directly.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
