---
url: https://planetscale.com/docs/neki/vitess-and-postgres
title: "Vitess And Postgres"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Neki vs Vitess

> Vitess concepts mapped to Neki's Postgres architecture

If you already know Vitess, Neki will look familiar. A router sits in front of shards, configuration defines data placement, and live workflows can copy and stream data for operations such as resharding while the database continues serving traffic. Cutover, failures, and client reconnects can still produce transient errors that applications must handle.

However, Neki is not Vitess with MySQL swapped for Postgres.
Many parts of Vitess's design are MySQL-specific.
Neki serves a similar purpose but implements routing and cluster operations for Postgres semantics.

## The shared model

| | Vitess | Neki |
| - | - | - |
| Client protocol | MySQL | Postgres |
| Query router | VTGate | [Router](https://planetscale.com/docs/neki/overview.md#routers) |
| Database in a shard | MySQL | Postgres |
| Routing model | Keyspaces, VSchema, and vindexes | [Data topology](https://planetscale.com/docs/neki/data-topology.md), shard groups, and shard indexes |
| Data movement | VReplication | [Replicator](https://planetscale.com/docs/neki/replication.md) workflows |
| Cluster operations | vtctld and related control plane | [Admin service](https://planetscale.com/docs/neki/overview.md#the-admin) |

Both systems route queries, control fanout, coordinate primary changes, and move data while continuing to serve eligible traffic.
The implementation for each system is designed with the underlying relational database in mind.

## Related comparisons

* [Neki vs PlanetScale Postgres](https://planetscale.com/docs/neki/coming-from-postgres.md)
* [PlanetScale Postgres vs Vitess](https://planetscale.com/docs/postgres-vs-vitess.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
