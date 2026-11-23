---
url: https://planetscale.com/docs/postgres/promote-branch
title: "Promote Branch"
description: ""
access_date: 2026-11-23T22:53:06.735Z
current_date: 2026-11-23T22:53:06.735Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Promote and demote a branch

> Promote a Postgres branch to production, or demote it back to development.

This page covers Postgres.

Neki version: [Neki](https://planetscale.com/docs/neki/promote-branch.md)

A development branch can use a production cluster size when promoted to
production. Promoting it changes who can connect, lets it become the default
branch, locks the [web console](https://planetscale.com/docs/postgres/web-console.md#enable-for-production-branches),
and adds a [maintenance window](https://planetscale.com/docs/postgres/cluster-configuration/maintenance-windows.md).

To resize a production branch to `PS-DEV` it must be demoted to development first.
When a development branch is promoted it will automatically select the next size
up (PS-5) however you can choose a larger size if needed.

## Promote a branch

On the branch page, select **Promote to production** in the cluster card.

From the dialog, choose a cluster size and a replica count for the default
shard configuration profile. It will default to `PS-5` unless the branch is
already on a production size.

`0` replicas is a single node. `2` to `8` is high availability. Metal sizes
need at least 2 replicas.

Promotion also:

* Adds a weekly maintenance window.
* Makes this branch the default if the current default is a development branch.

You can change the size and replica count later from the
[cluster configuration](https://planetscale.com/docs/postgres/cluster-configuration.md) page. That does not
change whether the branch is production.

## Demote a branch

Select **Demote to development** in the cluster card.

When demoting a branch, you can keep the current size or choose another one,
including `PS-DEV`. `PS-DEV` is a single node, so the replica count becomes `0`.

Demotion removes the weekly maintenance window. Roles and passwords stay the
same.

You cannot demote a branch that has a
[dedicated read replica](https://planetscale.com/docs/postgres/scaling/dedicated-read-replicas.md). A Scaler
Pro database also cannot demote its last production branch.

Changing the cluster size restarts the database. Direct connections and
connections through the local PgBouncer close.
[Dedicated primary PgBouncer](https://planetscale.com/docs/postgres/connecting/pgbouncer.md#dedicated-primary-pgbouncers)
connections stay open. Leaving the size unchanged does not restart the
database.

## CLI

```bash theme={null}
pscale branch promote <database> <branch> --cluster-size PS-10 --replicas 2
pscale branch demote <database> <branch> --cluster-size PS-DEV
```

`--cluster-size` and `--replicas` are optional. Leave one out to keep the
current value. A `PS-DEV` branch moves to `PS-5` when you omit
`--cluster-size`. Demote allows `PS-DEV`.

Changing the size or the replica count also needs permission to update the
database. See the [CLI reference](https://planetscale.com/docs/cli/branch.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
