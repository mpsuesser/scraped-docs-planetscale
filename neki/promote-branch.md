---
url: https://planetscale.com/docs/neki/promote-branch
title: "Promote Branch"
description: ""
access_date: 2026-11-23T22:53:06.735Z
current_date: 2026-11-23T22:53:06.735Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Promote and demote a branch

> Promote a Neki branch to production, or demote it back to development.

This page covers Neki.

Postgres version: [Postgres](https://planetscale.com/docs/postgres/promote-branch.md)

A development branch can use a production cluster size when promoted to
production. Promoting it changes who can connect, lets it become the default
branch, locks the [web console](https://planetscale.com/docs/neki/web-console.md#enable-for-production-branches),
and adds a [maintenance window](https://planetscale.com/docs/neki/cluster-configuration/maintenance-windows.md).

To resize a production branch to `PS-DEV` it must be demoted to development first.
When a development branch is promoted it will automatically select the next size
up (PS-10), however you can choose a larger size if needed.

Production branches run the router and admin in 3 cells. Development branches run
them in 1 cell.

## Promote a branch

On the branch page, click **Promote to production** in the cluster card.

From the dialog, choose a cluster size and a replica count for the default
shard configuration profile. It will default to `PS-10` unless the branch is
already on a production size.

`0` replicas is a single node while `2` to `8` is high availability. Metal sizes
require at least 2 replicas.

`NKR-DEV` routers moves to the recommended router size for the cluster size you
choose, and runs in 3 cells. Routers that are already a production size are not
changed, including their autoscaling settings. The dialog shows the router change and
the monthly cost.

Promotion also:

* Adds a weekly maintenance window.
* Makes this branch the default if the current default is a development branch.
* Moves the admin from 1 cell to 3 cells.

You can change the router size later on the branch clusters page. That does
not change whether the branch is production.

## Demote a branch

Select **Demote to development** in the cluster card.

When demoting a branch, you can keep the current size or choose another one,
including `PS-DEV`. `PS-DEV` is a single node, so the replica count becomes `0`.

Demoting to `PS-DEV` moves production routers back to `NKR-DEV`, in 1 cell.
Autoscaling settings are not changed. If you keep a production cluster size,
the routers reamin as they are.

Demotion removes the weekly maintenance window and moves the admin from 3
cells to 1 cell. Roles and passwords are unchanged.

You cannot demote a branch that has a [read-only replica](https://planetscale.com/docs/neki/read-only-replicas.md).
A Scaler Pro database also cannot demote its last production branch.

## CLI

```bash theme={null}
pscale branch promote <database> <branch> --cluster-size PS-10 --replicas 2
pscale branch demote <database> <branch> --cluster-size PS-DEV
```

`--cluster-size` and `--replicas` are optional. Leave one out to keep the
current value. A `PS-DEV` branch moves to `PS-10` when you omit
`--cluster-size`. Demote allows `PS-DEV`.

Changing the size, the replica count, or the router size also needs
permission to update the database. See the [CLI reference](https://planetscale.com/docs/cli/branch.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
