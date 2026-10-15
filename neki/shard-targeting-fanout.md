---
url: https://planetscale.com/docs/neki/shard-targeting-fanout
title: "Shard Targeting Fanout"
description: ""
access_date: 2026-10-15T11:57:20.597Z
current_date: 2026-10-15T11:57:20.597Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Shard targeting and fanout

> Limit how broadly statements route, choose where reads run, and target one shard

Neki routes each statement according to its [query plan](query-planning.md)
and the [data topology](data-topology.md). Session settings control three
parts of that routing: how many shards a statement can reach, which type of
Postgres instance serves reads, and whether data queries go to one shard
directly.

## Controlling fanout

The topology determines which shards contain rows.
The fanout component of a query plan specifies how broadly a statement will route:

| Level | Permitted routing shape |
| - | - |
| `single` | A plan with single-shard routing and at most one shard-touching operation |
| `multi` | `single`, plus plans limited to a bounded set of shards |
| `scatter` | Any plan, including one that reaches every shard in a group |

An `IN` predicate on the shard key produces a `Route [IN]`, as shown in [Query
planning](query-planning.md#explain-neki_plan), and has `multi` fanout.
Its values may resolve to one shard or several, but the plan is limited to the shards holding those values.

### The `__neki.fanout` setting

Set `__neki.fanout` for a session to reject `SELECT` and data-changing
statements whose routing shape exceeds the selected level:

```sql theme={null}
SET __neki.fanout = 'single';
```

The default is `scatter`. A statement that exceeds the setting fails instead of running:

```text theme={null}
statement's fan-out (scatter) exceeds __neki.fanout (single)
```

Use a stricter setting in development or CI to catch queries that route more
broadly than intended. Set `single` for request paths expected to use a
single-shard routing shape, or `multi` when a bounded set of destinations is
acceptable. Allow `scatter` for deliberate whole-group work.

The setting also applies to `EXPLAIN (NEKI_PG_PLAN, ANALYZE)` because `ANALYZE`
executes the underlying statement.

This setting does not apply to DDL. Fanout required to maintain [reference tables
and GSIs](reference-tables-and-gsis.md) is also exempt.

## Choosing where reads run

Read targeting selects which type of Postgres instance serves a session's read
queries. The default target is `primary`.

Set `__neki.target` to use another target:

```sql theme={null}
SET __neki.target = 'replica';
```

| Target | Use |
| - | - |
| `primary` | Send reads to the shard primary. This is the only target that accepts DML |
| `replica` | Send reads to replicas used for live traffic |
| `rdonly` | Send reads to `rdonly` instances used for long-running work |

The following settings control how Neki chooses a `replica` or `rdonly`
instance:

| Setting | Values | Default |
| - | - | - |
| `__neki.replica_recency` | `prefer` favors the freshest tier, `require` allows only that tier, and `off` does not order by lag | `prefer` |
| `__neki.replica_locality` | `prefer` favors the router's cell, `require` allows only that cell, and `off` does not order by cell | `prefer` |
| `__neki.replica_affinity` | `session` favors the same replica for later reads, while `none` chooses again for each statement | `none` |

Neki excludes a replica when its lag is unknown or above the maximum allowed
lag. A `require` policy can narrow the candidates further. The query fails if no
candidate remains.

These settings cannot change during a transaction. Set them in separate
statements before starting the transaction.

## Targeting one shard directly

Direct shard targeting sends data queries to one shard UID without using the
data topology to route them.

```sql theme={null}
SET __neki.shard = 'shard-a';
```

Data statements follow these rules while `__neki.shard` is set:

| Statement | Behavior |
| - | - |
| `SELECT`, `INSERT`, `UPDATE`, `DELETE`, and `MERGE` | Forward to the targeted shard |
| `SET`, `SHOW`, transaction control, and prepared-statement control | Follow the normal router path |
| Schema-changing DDL, such as `CREATE TABLE`, `ALTER TABLE`, `DROP TABLE`, `TRUNCATE`, and `SELECT INTO` | Reject |
| `COPY`, `DO`, and unsupported statement types | Reject |

Neki rejects a schema change while a direct shard target is set because schema
changes run across the cluster rather than on one shard. Use a separate
connection without `__neki.shard` to run the change cluster-wide. Publication
DDL and maintenance, privilege, and annotation statements still follow the
normal router path and can run across every shard.

While a shard target is set, a data query cannot also call a router-managed
function such as `current_setting`, `set_config`, or `nextval`. Neki rejects
the statement because it cannot forward the table access and evaluate the
function in the router.

You cannot change `__neki.shard` during a transaction. Clear the target to
restore normal routing:

```sql theme={null}
RESET __neki.shard;
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
