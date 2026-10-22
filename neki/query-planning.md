---
url: https://planetscale.com/docs/neki/query-planning
title: "Query Planning"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Query planning

> How Neki plans a statement, and how to inspect its plan with EXPLAIN

A query plan defines where each part of a SQL statement runs. It contains
routes that send work to shards and, when needed, router operations that combine
shard results.

## How a statement becomes a plan

Incoming queries to a Neki database cluster are planned after being parsed at the router.

The planner builds the plan from four inputs:

* **The SQL statement.** The statement defines the tables, predicates, and operations that the plan must cover.
* **The database schema.** The schema defines the tables, columns, types, and indexes that the statement refers to.
* **The data topology.** The [data topology](https://planetscale.com/docs/neki/data-topology.md) defines which shards hold the rows of each table.
* **Statistics.** Statistics collected from the shards give the planner row estimates to compare alternative plans. See [Cost model, cardinality estimation, and statistics](#cost-model-cardinality-estimation-and-statistics).

### Query plan cache and protocols

The query protocol determines whether two statements can share a plan. With
Postgres's simple query protocol, Neki replaces eligible literal values with
parameters before looking up a plan. `user_id = 1337` and `user_id = 999`, for
example, both become `user_id = $1` and can share a plan.

The value for `$1` is bound at execution, when it can direct the route to a different shard.

> [!NOTE]
>
> Preparing a statement does not pin its plan. The router looks it up again
> using the current `search_path` and session role, and may rebuild it if the
> cached copy was evicted or invalidated by a schema, topology, or statistics
> change.

## EXPLAIN in Neki

A Neki plan has two levels. At the router level, Neki routes work to shards and
combines the results. At the shard level, each shard's Postgres plans the SQL
that Neki sends to it. The three `EXPLAIN` modes differ in which levels they
show:

| Mode | Shows |
| - | - |
| `EXPLAIN` | Only the shard level: a Postgres plan from one shard |
| `EXPLAIN (NEKI_PLAN)` | Only the router level: Neki's routes and router operations |
| `EXPLAIN (NEKI_PG_PLAN)` | Both levels: Neki's plan with the Postgres plan from each shard that a route involves |

`COSTS` works in all three modes. `COSTS OFF` removes the cost estimates from
Neki's operators and from the shard-level Postgres plans.

`VERBOSE`, `BUFFERS`, `SETTINGS`, `WAL`, and `MEMORY` have no effect on Neki's own
operators. `EXPLAIN` and `EXPLAIN (NEKI_PG_PLAN)` pass them as written to the
shard-level Postgres `EXPLAIN`. `EXPLAIN (NEKI_PLAN)` accepts them and ignores
them.

All four output formats (`TEXT`, `JSON`, `XML`, and `YAML`) are supported in
all three modes. The default format is `TEXT`.

The examples in this section use the [example schema](https://planetscale.com/docs/neki/example-schema.md), where `public.orders` belongs to the `user_data` shard group and uses `user_id` as its primary shard index.

### `EXPLAIN`

A plain Postgres `EXPLAIN` works when the whole statement runs on the shards
with no router operations. The plan is then a single route, which can also be
a route that scatters to every shard in a group, because every shard runs the
same SQL. Neki sends the `EXPLAIN` to one of the route's shards and returns
that shard's output unchanged. The output is the plan of a real Postgres, with
no Neki-specific content:

```sql theme={null}
EXPLAIN
SELECT order_id
FROM public.orders
WHERE user_id = 1337;
```

```text theme={null}
Seq Scan on orders  (cost=0.00..2.00 rows=1 width=8)
  Filter: (user_id = 1337)
```

When the plan has router operations, Neki rejects the statement with SQLSTATE
`NK017`:

```text theme={null}
ERROR:  This statement requires Neki router processing
HINT:  Use `EXPLAIN (NEKI_PG_PLAN)` (with ANALYZE to execute) to see Neki's plan
```

`EXPLAIN ANALYZE` executes the statement. Neki accepts it with a plain
`EXPLAIN` only when the plan sends the whole statement to exactly one shard.

### `EXPLAIN (NEKI_PLAN)`

`EXPLAIN (NEKI_PLAN)` shows Neki's plan: the routes that send work to shards,
and the router operations that combine their results. Without `ANALYZE`, it does not
contact the shards. The output shows only router-level plan nodes. The route node is the
boundary: it shows the SQL that runs on the shards, but not the shard-level
plans.

An equality predicate on `orders.user_id` produces one route:

```sql theme={null}
EXPLAIN (NEKI_PLAN)
SELECT order_id
FROM public.orders
WHERE user_id = 1337;
```

```text theme={null}
Route [EqualUnique]  (cost=0.00..1100.00 rows=100 width=8)
  Query: SELECT order_id FROM public.orders WHERE user_id = $1
  ShardGroup: user_data
  Values: $1
```

The `Query` field uses a reusable parameterized form of the SQL, so the literal `1337` appears as `$1`.
`Values: $1` is the parameter the route resolves, and `ShardGroup: user_data` names the topology group that receives the route.
`Route [EqualUnique]` is the single-shard routing choice.

An `IN` predicate produces a route for the selected values:

```sql theme={null}
EXPLAIN (NEKI_PLAN)
SELECT order_id
FROM public.orders
WHERE user_id IN (42, 999);
```

```text theme={null}
Collapse  (cost=0.00..2500.00 rows=500 width=8)
  ->  Route [IN]  (cost=0.00..2500.00 rows=500 width=8)
        Query: SELECT order_id FROM public.orders WHERE user_id = ANY($1)
        ShardGroup: user_data
        Values: $1
```

`Route [IN]` narrows the query to the shards holding the listed values. It has
`multi` [fanout](https://planetscale.com/docs/neki/shard-targeting-fanout.md#controlling-fanout).
`Collapse` indicates where Neki turns those responses into one result.

An aggregate without a usable shard-key predicate scatters across the group:

```sql theme={null}
EXPLAIN (NEKI_PLAN)
SELECT count(*)
FROM public.orders;
```

```text theme={null}
Aggregate [Ordered]  (cost=0.00..2002.00 rows=1 width=8)
  ->  Collapse  (cost=0.00..2002.00 rows=2 width=8)
        ->  Route [Scatter]  (cost=0.00..2002.00 rows=2 width=8)
              Query: SELECT count(*) FROM public.orders
              ShardGroup: user_data
```

`Route [Scatter]` indicates this is a scatter-gather query, which requires contacting all shards in the shard group to complete the query.
Each shard computes a partial count, and the router-side `Aggregate` adds the partial results before returning the final count.

As a shard group grows, the number of shards used by scatter queries increases.
This may be acceptable for occasional full-group aggregates and other whole-table operations, but scatter-gather execution of frequent queries should be avoided because it increases utilization of database and network resources.
If you expect a query to be single-shard but are seeing `Route [Scatter]` in the query plan, check for a suitable shard-key predicate or consider using a [global secondary index (GSI) lookup](https://planetscale.com/docs/neki/reference-tables-and-gsis.md#gsi-mapping).

### `EXPLAIN (NEKI_PG_PLAN)`

`EXPLAIN (NEKI_PG_PLAN)` shows Neki's plan together with the Postgres plan
from each shard that a route involves. A Postgres plan shows how a shard plans
to run the SQL that Neki sends to it. The output shows both router-level and
shard-level plan nodes, and a scatter route shows a plan for each shard in the
group. Neki designs this mode for maximal compatibility with Postgres, so tools that
read Postgres `EXPLAIN` output can also read Neki plans.

Without `ANALYZE`, Neki does not execute the statement. For each route, Neki
sends a plain Postgres `EXPLAIN` to every shard that the route involves:

| Route | Shards that receive the `EXPLAIN` |
| - | - |
| Scatter | Every shard in the shard group |
| Equality or `IN` | The shards that hold the listed values |
| Any shard, such as a read from a reference table | Every candidate shard |

Each route shows its shard plans under one of two labels. `All shard plans`
(`"ShardPlans": {"All": [...]}` in the structured formats) means that every
listed shard runs the route's SQL. `Any one shard plan` (`"Any"`) means that
the route runs on one of the listed shards, selected arbitrarily.

A scatter read from `users` in the [example schema](https://planetscale.com/docs/neki/example-schema.md) shows the plan of both shards in the group:

```sql theme={null}
EXPLAIN (NEKI_PG_PLAN)
SELECT user_id, name
FROM users;
```

```text theme={null}
Collapse  (cost=0.00..3000.00 rows=1000 width=151)
  ->  Route [Scatter]  (cost=0.00..3000.00 rows=1000 width=151)
        Query: SELECT user_id, name FROM public.users
        ShardGroup: user_data
        All shard plans
        ->  Shard shard-a  (cost=0.00..0.00 rows=0 width=0)
              ->  Seq Scan on users  (cost=0.00..1.20 rows=20 width=15)
        ->  Shard shard-b  (cost=0.00..0.00 rows=0 width=0)
              ->  Seq Scan on users  (cost=0.00..1.30 rows=30 width=15)
```

When the whole plan is one route on one shard, Neki omits the route node. The
output names the shard on the first line, followed by the shard's Postgres
plan, similar to the output of a plain `EXPLAIN`:

```sql theme={null}
EXPLAIN (NEKI_PG_PLAN)
SELECT user_id, name
FROM users
WHERE user_id = 42;
```

```text theme={null}
Shard shard-a
  Seq Scan on users  (cost=0.00..1.25 rows=1 width=15)
    Filter: (user_id = 42)
```

Some routes take values that exist only after the statement starts to run, for
example the inner side of a correlated subquery. Without `ANALYZE`, Neki
rejects `NEKI_PG_PLAN` for such a plan. Use `ANALYZE`, or drop `NEKI_PG_PLAN`
to see only Neki's plan.

With `ANALYZE`, Neki executes the statement. The output adds Neki's actual row
counts and timings, and per-shard statistics for each route. A route whose rows
go directly to the client shows each shard's `EXPLAIN ANALYZE` output. A route
whose rows feed a router operation, such as one input of a join, shows a plain
Postgres `EXPLAIN` from each shard that the route used.

In the following example, the route's rows feed the router-side `Aggregate`,
so each shard shows a plain `EXPLAIN` with its statistics:

```sql theme={null}
EXPLAIN (NEKI_PG_PLAN, ANALYZE)
SELECT sum(total_amount)
FROM orders
WHERE created_at > '2026-09-09';
```

```text theme={null}
Aggregate [Ordered]  (cost=0.00..2002.00 rows=1 width=32) (actual time=0.236..0.238 rows=1 loops=1)
  RowsTotal: 1
  CostAtActualRows: Total=2002 RemoteCalls=2 RowsProcessed=2 NetworkRows=2
  ->  Collapse  (cost=0.00..2002.00 rows=2 width=32) (actual time=0.217..0.220 rows=2 loops=1)
        RowsTotal: 2
        CostAtActualRows: Total=2002 RemoteCalls=2 NetworkRows=2
        ->  Route [Scatter]  (cost=0.00..2002.00 rows=2 width=32) (actual time=0.216..0.217 rows=2 loops=1)
              RowsTotal: 2
              RemoteCalls: 2
              NetworkBytesIn: 102
              AvgWidth: 51
              CostAtActualRows: Total=2002 RemoteCalls=2 NetworkRows=2
              Query: SELECT sum(total_amount) FROM public.orders WHERE created_at > $1::timestamp without time zone
              ShardGroup: user_data
              ->  Shard shard-a  (cost=0.00..0.00 rows=0 width=0)
                    Shard Stats: Executions=1 TimeMs=0.21 RowsOut=1 BytesIn=51
                    ->  Aggregate  (cost=2.45..2.46 rows=1 width=32)
                          ->  Seq Scan on orders  (cost=0.00..2.40 rows=18 width=5)
                                Filter: (created_at > ('2026-09-09'::cstring)::timestamp without time zone)
              ->  Shard shard-b  (cost=0.00..0.00 rows=0 width=0)
                    Shard Stats: Executions=1 TimeMs=0.215 RowsOut=1 BytesIn=51
                    ->  Aggregate  (cost=4.17..4.18 rows=1 width=32)
                          ->  Seq Scan on orders  (cost=0.00..4.10 rows=26 width=5)
                                Filter: (created_at > ('2026-09-09'::cstring)::timestamp without time zone)
Planning Time: 0.192 ms
Execution Time: 0.239 ms
```

> [!WARNING]
>
> `ANALYZE` executes the statement being explained. This is especially risky
> for data-changing statements: an `INSERT`, `UPDATE`, `DELETE`, or `MERGE`
> under `ANALYZE` changes rows on every shard that the plan reaches, and the
> changes stay.

[EXPLAIN output reference](https://planetscale.com/docs/neki/explain-reference.md) shows the complete
`NEKI_PLAN` and `NEKI_PG_PLAN` output of a query with router operations, in
every format.

### Cost model, cardinality estimation, and statistics

> *A perfect search over bad estimates is just a very efficient way to find the wrong plan.*

Neki's query planner uses statistics collected from the shards. The statistics
must exist on the shards before Neki can collect them, and keeping them current
is your responsibility. Postgres has many kinds of statistics. The [Postgres
documentation on planner statistics](https://www.postgresql.org/docs/current/planner-stats.html)
describes them.

The planner estimates the number of rows that each part of a plan produces.
These cardinality estimates come from the collected statistics. The cost model
then uses the estimates to compare the alternative plans and choose the
cheapest one.

Neki collects the following statistics from the catalog of each shard's
Postgres. This list grows as Neki collects more kinds of statistics.

* The estimated row count of each table.
* The number of distinct values (`n_distinct`) and the fraction of NULL values
  (`null_frac`) of each column that is part of an index, or of a primary key,
  foreign key, or unique constraint.

A collector on each shard reads the catalog periodically and sends
changes to the routers. A significant change invalidates the cached plans that
depend on the changed statistics.

Postgres writes statistics when `ANALYZE`, autoanalyze, `VACUUM`, or
`CREATE INDEX` runs. A freshly loaded table has no statistics until one of these
runs. Until then, the planner uses default estimates. `ANALYZE` and `VACUUM`
sent through a router run on every shard.

[Data topology](https://planetscale.com/docs/neki/data-topology.md#choosing-a-shard-index) recommends a
Postgres index on the shard-key columns, and [Best
practices](https://planetscale.com/docs/neki/best-practices.md) recommends that you keep their statistics
current. Indexes and current statistics give the planner the estimates it needs
to find a good plan. An index also adds its columns to the set whose statistics
Neki collects.

`EXPLAIN (NEKI_PG_PLAN)` shows what the planner does with its inputs. Creating
and dropping indexes, and running `ANALYZE`, change what the planner has to
work with. Repeat the two steps to keep your queries fast:

1. Inspect the plan with `EXPLAIN (NEKI_PG_PLAN)`.
2. Change the indexes or refresh the statistics, then inspect the plan again.

Insights helps in this process. Use [Query
Insights](https://planetscale.com/docs/neki/monitoring/query-insights.md) to observe the workload, and [schema
recommendations](https://planetscale.com/docs/neki/monitoring/schema-recommendations.md) for DDL suggestions
based on production telemetry.

## Statement types

Neki plans read queries (`SELECT`) and data-changing statements (`INSERT`,
`UPDATE`, `DELETE`, and `MERGE`) the standard way, which [EXPLAIN in
Neki](#explain-in-neki) describes. When a plan reaches several shards, Neki
sends the shard requests in parallel and combines the responses into one
Postgres result.

Some results require more than concatenating rows from each shard. Neki can
combine partial aggregates, apply ordering and limits across shards, or
complete joins that cannot be pushed into the shards.

Neki plans a statement the same way inside and outside an explicit transaction. The
transaction limits only how the plan runs, which
[Transactions](https://planetscale.com/docs/neki/transactions.md) describes.

The statement types in the following sections have their own planning.

### DDL

Neki keeps the same schema on every shard, so data definition language (DDL)
sent through a router runs on every managed shard.
Multi-shard DDL is not atomic across the deployment. A failure can leave the
change applied on some shards but not others.

Managed [schema-change workflows](https://planetscale.com/docs/neki/schema-changes.md) add
readiness checks and coordinate completion through online or direct execution
paths.

### COPY

Neki plans `COPY` on its own path because the rows arrive as a data stream
after the statement, not in the statement text. For `COPY FROM` into a sharded
table, Neki routes each incoming row to a shard.

`COPY FROM` can route rows into a sharded table when the input includes the
columns needed by its primary shard indexes. It is rejected when that table has
an enabled GSI because COPY does not maintain GSI lookup rows. Keep the GSI
disabled during the copy, then backfill and verify the lookup table before
enabling it.

Client `COPY TO STDOUT` works for a sharded table, a reference table, and a
materialized view, including binary. File `COPY` remains unsharded-only.

### Cursors

Neki plans the query of a `DECLARE` statement the standard way, then builds the
cursor from the plan:

* When the plan is a single route, each shard that the route resolves to opens
  its own Postgres cursor.
* When the plan has router operations, the router serves the cursor. It runs
  the plan and returns rows as `FETCH` statements ask for them.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
