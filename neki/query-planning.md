---
url: https://planetscale.com/docs/neki/query-planning
title: "Query Planning"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

A query plan defines where each part of a SQL statement runs. It contains routes that send work to shards and, when needed, router operations that combine shard results. Neki builds the plan from the statement and the current [data topology](data-topology.md).

## How a statement becomes a plan

Incoming queries to a Neki database cluster are planned after being parsed at the router.

The query protocol determines whether two statements can share a plan. With Postgres’s simple query protocol, Neki replaces eligible literal values with parameters before looking up a plan. `tenant_id = 12` and `tenant_id = 13`, for example, both become `tenant_id = $1` and can share a plan.

The value for `$1` is bound at execution, when it can direct the route to a different shard.

Preparing a statement does not pin its plan. The router looks it up again using the current `search_path` and session role, and may rebuild it if the cached copy was evicted or invalidated by a schema or topology change.

## Reading a query plan

Executing `EXPLAIN (NEKI_PLAN)` along with a query on a Neki database cluster shows Neki’s complete query plan. It returns JSON unless another format is requested, so add `FORMAT TEXT` for a compact, human-readable tree. `COSTS OFF` removes estimates that are not needed to identify the routing shape.

The examples below assume `public.events` belongs to the `tenant_data` shard group and uses `tenant_id` as its primary shard index.

An equality predicate on `events.tenant_id` produces one route:

```sql
EXPLAIN (NEKI_PLAN, COSTS OFF, FORMAT TEXT)
SELECT event_id
FROM public.events
WHERE tenant_id = 4821;
```

```text
Route [EqualUnique]
  Query: SELECT event_id FROM public.events WHERE tenant_id = $1
  ShardGroup: tenant_data
  Values: $1
```

The `Query` field uses a reusable parameterized form of the SQL, so the literal `4821` appears as `$1`. `Values: $1` is the parameter the route resolves, and `ShardGroup: tenant_data` names the topology group that receives the route. `Route [EqualUnique]` is the single-shard routing choice.

An `IN` predicate produces a route for the selected values:

```sql
EXPLAIN (NEKI_PLAN, COSTS OFF, FORMAT TEXT)
SELECT event_id
FROM public.events
WHERE tenant_id IN (4821, 9374);
```

```text
Collapse
└── Route [IN]
      Query: SELECT event_id FROM public.events WHERE tenant_id = ANY($1)
      ShardGroup: tenant_data
      Values: $1
```

`Route [IN]` narrows the query to the shards holding the listed values. `Collapse` indicates where Neki turns those responses into one result.

An aggregate without a usable shard-key predicate scatters across the group:

```sql
EXPLAIN (NEKI_PLAN, COSTS OFF, FORMAT TEXT)
SELECT count(*)
FROM public.events;
```

```text
Aggregate [Ordered]
└── Collapse
    └── Route [Scatter]
          Query: SELECT count(*) FROM public.events
          ShardGroup: tenant_data
```

`Route [Scatter]` indicates this is a scatter-gather query, which requires contacting all shards in the shard group to complete the query. Each shard computes a partial count, and the router-side `Aggregate` adds the partial results before returning the final count.

As a shard group grows, the number of shards used by scatter queries increases. This may be acceptable for occasional full-group aggregates and other whole-table operations, but scatter-gather execution of frequent queries should be avoided because it increases utilization of database and network resources. If you expect a query to be single-shard but are seeing `Route [Scatter]` in the query plan, check for a suitable shard-key predicate or consider using a global secondary index (GSI) lookup.

A plain Postgres `EXPLAIN` works only when the Neki plan reduces to a single route. Plans with router-side operators are rejected with a pointer to `NEKI_PLAN`.

### Include a Postgres plan from shards

A Postgres plan shows how a shard plans to run the SQL that Neki sends to it. Use `NEKI_PG_PLAN` with `NEKI_PLAN` to include this information inside each route:

```sql
EXPLAIN (NEKI_PLAN, NEKI_PG_PLAN, COSTS OFF, FORMAT TEXT)
SELECT event_id
FROM public.events
WHERE tenant_id = 4821;
```

Without `ANALYZE`, Neki chooses one shard that the route can use and shows that shard’s plan. If the route needs values produced while the query runs, use `ANALYZE`. Neki then executes the statement and shows a Postgres plan for every shard the route used.

`ANALYZE` executes the statement being explained.

## Controlling fanout

The topology determines which shards contain rows. The fanout component of a query plan specifies how broadly a statement will route:

| Level | Permitted routing shape |
| --- | --- |
| `single` | A plan with single-shard routing and at most one shard-touching operation |
| `multi` | `single`, plus plans limited to a bounded set of shards |
| `scatter` | Any plan, including one that reaches every shard in a group |

The `Route [IN]` example above has `multi` fanout. Its values may resolve to one shard or several, but the plan is limited to the shards holding those values.

### The \_\_neki.fanout setting

Set `__neki.fanout` for a session to reject `SELECT` and data-changing statements whose routing shape exceeds the selected level:

```sql
SET __neki.fanout = 'single';
```

The default is `scatter`. A statement that exceeds the setting fails instead of running:

```text
statement's fan-out (scatter) exceeds __neki.fanout (single)
```

Use a stricter setting in development or CI to catch queries that route more broadly than intended. Set `single` for request paths expected to use a single-shard routing shape, or `multi` when a bounded set of destinations is acceptable. Allow `scatter` for deliberate whole-group work.

The setting also applies to `EXPLAIN (NEKI_PLAN, ANALYZE)` because `ANALYZE` executes the underlying statement.

This setting does not apply to DDL. Fanout required to maintain [reference tables and GSIs](reference-tables-and-gsis.md) is also exempt.

## Choosing where reads run

Read targeting selects which type of Postgres instance serves a session’s read queries. The default target is `primary`.

Set `__neki.target` to use another target:

```sql
SET __neki.target = 'replica';
```

| Target | Use |
| --- | --- |
| `primary` | Send reads to the shard primary. This is the only target that accepts DML |
| `replica` | Send reads to replicas used for live traffic |
| `rdonly` | Send reads to `rdonly` instances used for long-running work |

The following settings control how Neki chooses a `replica` or `rdonly` instance:

| Setting | Values | Default |
| --- | --- | --- |
| `__neki.replica_recency` | `prefer` favors the freshest tier, `require` allows only that tier, and `off` does not order by lag | `prefer` |
| `__neki.replica_locality` | `prefer` favors the router’s cell, `require` allows only that cell, and `off` does not order by cell | `prefer` |
| `__neki.replica_affinity` | `session` favors the same replica for later reads, while `none` chooses again for each statement | `none` |

Neki excludes a replica when its lag is unknown or above the maximum allowed lag. A `require` policy can narrow the candidates further. The query fails if no candidate remains.

These settings cannot change during a transaction. Set them in separate statements before starting the transaction.

## Targeting one shard directly

Direct shard targeting sends data queries to one shard UID without using the data topology to route them.

```sql
SET __neki.shard = 'shard-a';
```

Data statements follow these rules while `__neki.shard` is set:

| Statement | Behavior |
| --- | --- |
| `SELECT`, `INSERT`, `UPDATE`, `DELETE`, and `MERGE` | Forward to the targeted shard |
| `SET`, `SHOW`, transaction control, and prepared-statement control | Follow the normal router path |
| Schema-changing DDL, such as `CREATE TABLE`, `ALTER TABLE`, `DROP TABLE`, `TRUNCATE`, and `SELECT INTO` | Reject |
| `COPY`, `DO`, and other unsupported statement types | Reject |

Neki rejects a schema change while a direct shard target is set because schema changes run across the cluster rather than on one shard. Use a separate connection without `__neki.shard` to run the change cluster-wide. Publication DDL and maintenance, privilege, and annotation statements still follow the normal router path and can run across every shard.

While a shard target is set, a data query cannot also call a router-managed function such as `current_setting`, `set_config`, or `nextval`. Neki rejects the statement because it cannot forward the table access and evaluate the function in the router.

You cannot change `__neki.shard` during a transaction. Clear the target to restore normal routing:

```sql
RESET __neki.shard;
```

## Executing across shards

When a query plan reaches several shards, it sends the shard requests in parallel and combines the responses into one Postgres result.

### COPY routing

`COPY FROM` can route rows into a sharded table when the input includes the columns needed by its primary shard indexes. It is rejected when that table has an enabled GSI because COPY does not maintain GSI lookup rows. Keep the GSI disabled during the copy, then backfill and verify the lookup table before enabling it.

### Combining results

Some results require more than concatenating rows from each shard. Neki can combine partial aggregates, apply ordering and limits across shards, or complete joins that cannot run on one shard.

A query without `ORDER BY` has no cross-shard row-order guarantee. Add an explicit order when the application depends on result sequence.

### DDL fans out

Data definition language (DDL) sent through a router runs synchronously on every managed shard. Multi-shard DDL is not atomic across the deployment. A failure can leave the change applied on some shards but not others.

Managed [schema-change workflows](schema-changes.md) add readiness checks and coordinate completion through online or direct execution paths.

## Transactions across shards

Routing one statement to several Postgres shards does not turn those shards into one transactional database.

### Reads do not share a snapshot

An ordinary multi-shard read does not establish one shared Postgres snapshot across its destinations. Concurrent writes can therefore cause different shards to reflect different points in time within the same result.

### Multi-shard writes are not atomic

Neki commits each shard separately. It does not use two-phase commit to make every shard succeed or fail together. If one shard fails after another commits, the statement can leave different outcomes on the participating shards.

### Enforce a one-shard transaction

The default transaction routing mode is `multi`, which permits a transaction to reach more than one shard without making its commit atomic. Set the mode to `single` before starting a transaction when the application wants Neki to prevent that transaction from expanding to another shard:

```sql
SET __neki.tx_mode = 'single';
BEGIN;
```

Distributed and atomic cross-shard transactions are not yet supported. Keep a transaction on one shard when its writes must commit or roll back together.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
