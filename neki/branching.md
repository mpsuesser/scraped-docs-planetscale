---
url: https://planetscale.com/docs/neki/branching
title: "Branching"
description: ""
access_date: 2026-10-20T18:26:07.257Z
current_date: 2026-10-20T18:26:07.257Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Branching

> Create development branches, restore from backups, and run production with high availability.

A Neki branch is an isolated database environment with its own routers,
Postgres instances, data, configuration, backups, and billing. Use production
branches for serving workloads and development branches for disposable testing.

## Development branches start empty

Creating a Neki development branch provisions a new, isolated environment. It
does not copy the parent branch's schema or data. Apply your schema migrations
to the new branch and load only the test data you need.

To create a branch containing existing data, restore a [backup](backups.md)
to a new branch instead of creating an ordinary development branch.

## Create a development branch

**Step 1**

From the PlanetScale dashboard, select your Neki database.

**Step 2**

Open the branch menu and select **New branch**.

**Step 3**

Choose **Development**, enter a branch name, and review the selected region and estimated price.

**Step 4**

Select **Create branch** and wait for the branch to become ready.

**Step 5**

Apply your application's schema migrations and seed data.

The new branch uses development shard and router SKUs. Its infrastructure and
usage are billed independently from the parent. See [Neki pricing](pricing.md).

## Work with an isolated branch

Connections, roles, configuration profiles, parameters, extensions, and data
topology are branch-scoped. Verify the selected branch before changing its
configuration or running DDL.

An empty development branch is useful for testing schema setup from scratch.
See [Development environments](development-environments.md) for a repeatable
setup and guidance for production-like data.

## Move development data to production

To keep the data from a development branch, create a backup and restore it to a
new branch with production configuration. See [Restore a
backup](backups.md#restore-a-backup) for the required configuration-profile
and router sizes.

## Delete a branch

Deleting a branch removes its database infrastructure and data. The default
branch cannot be deleted while other branches remain. Confirm that any backup
or export you need has completed before deletion.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
