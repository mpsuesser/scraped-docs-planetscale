---
url: https://planetscale.com/docs/neki/platform-preview-limitations
title: "Platform Preview Limitations"
description: ""
access_date: 2026-10-27T16:16:59.369Z
current_date: 2026-10-27T16:16:59.369Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Platform preview limitations

> Postgres features and query shapes that Neki does not support during Platform Preview

> [!NOTE]
>
> Neki is currently in Platform Preview. Platform Preview features are “Beta Features” under the PlanetScale Terms of Service or your applicable agreement with PlanetScale. Accordingly, Neki is subject to the limitations and disclaimers applicable to Beta Features and is not covered by any service level agreement.

During the Platform Preview, Neki's router does not support the following
Postgres features or query shapes. These broad limits can change how an
application connects, writes SQL, or manages a shard. The planner can reject
more specific query combinations with a `not supported` or `not implemented`
error. [Neki error codes](https://planetscale.com/docs/neki/error-codes.md) lists these rejections, the query
shapes that trigger them, and what to do instead.

## High availability

Single-node, non-high-availability configurations are not supported during
Platform Preview.

## SQL and relation limits

The router rejects the following operations:

* `SELECT ... INTO`.
* `INTERSECT` and `EXCEPT` when Neki must plan a query for an application table.
  `UNION` is supported. This limit does not apply to statements that Neki
  forwards unchanged, such as direct-shard statements.
* `COMMIT AND CHAIN` and `ROLLBACK AND CHAIN`.
* Cross-database object references.
* Reading a Postgres inheritance parent without `ONLY` when it has child
  tables.
* A `search_path` that includes `pg_temp`.

## COPY limits

`COPY` must use the simple query protocol and must be the only statement in the
query. Neki rejects `COPY` over the extended query protocol.

The router supports `COPY FROM STDIN` and client `COPY TO STDOUT` (text, CSV,
and binary) for unsharded tables, sharded tables, and reference tables. Client
`COPY TO STDOUT` also works from a materialized view. For a sharded table,
include the single-column shard key in the copied columns. Sharded `COPY FROM`
does not support `ON_ERROR`, `REJECT_LIMIT`, or `LOG_VERBOSITY`.

File-based `COPY` supports unsharded tables only. Neki rejects `COPY FROM
PROGRAM`, `COPY TO PROGRAM`, `COPY (SELECT ...) TO`, and `COPY FROM` with a
`WHERE` clause.

When `COPY FROM` omits an identity column or a column with a sequence-backed or
router-only default, supply that column's values explicitly.

## User-defined text search

Neki supports Postgres's built-in text-search configurations and
dictionaries. During Platform Preview, the router rejects creating a
user-defined text-search configuration or dictionary and rejects changes to
their definitions. Router-side text-search evaluation cannot use those custom
definitions.

## Cluster-level objects

Neki rejects cluster-level objects that depend on storage or code local to one
Postgres host. This includes:

* Creating a tablespace or moving a database into a tablespace.
* Creating Postgres large objects.
* Creating a procedural language with a custom handler. Built-in languages and
  languages installed by a supported extension remain available.
* Loading a shared library with `LOAD`.

## Extensions that need shard targeting

The router rejects the extension calls in this section when Neki must plan
them, or returns the result of one shard only. Each call acts on one Postgres
server, or reports the state of one server. To use these calls, target one
shard directly:

1. Get the shard UIDs from `__neki.list_shards()`.
2. Set `__neki.shard` to one shard UID. Neki forwards each call to that shard
   unchanged.
3. Run the call. The result covers the targeted shard only.
4. Repeat steps 2 and 3 for each shard that the result must cover.
5. Reset `__neki.shard` to restore normal routing.

```sql theme={null}
SELECT uid, name FROM __neki.list_shards();
SET __neki.shard = '<shard-uid>';
-- Run the extension call here.
RESET __neki.shard;
```

See [Targeting one shard
directly](https://planetscale.com/docs/neki/shard-targeting-fanout.md#targeting-one-shard-directly).

### pg\_squeeze

The router rejects a `squeeze.squeeze_table()` call when Neki must plan it.
The call squeezes the table on the targeted shard only. Repeat it for each
shard that holds rows of the table.

### pgrowlocks

The router rejects a `pgrowlocks()` call when Neki must plan it. On the
targeted shard, the call shows the locked rows of the table on that shard
only. Repeat it for each shard that holds rows of the table.

### pgstattuple

The router rejects the `pgstattuple` functions, for example `pgstattuple()`,
when Neki must plan them. On the targeted shard, a call returns the statistics
of the rows on that shard only. Repeat it for each shard that holds rows of
the table.

To get the statistics of the full table, add the counts and the sizes from all
shards. Do not add the percentages. Calculate each percentage again from the
added values.

### hypopg

The router rejects `hypopg_create_index()` when Neki must plan it. On the
targeted shard, a plain `EXPLAIN` shows if the planner of that shard uses the
hypothetical index.

A hypothetical index exists only on one Postgres connection of the targeted
shard. Neki reuses each connection for many client sessions. A `ROLLBACK`
does not remove a hypothetical index. Thus, do all steps in one
transaction:

1. Set `__neki.shard` to one shard UID, before the transaction starts.
2. In the transaction, create the hypothetical index with
   `hypopg_create_index()`.
3. Run a plain `EXPLAIN` of the query.
4. Remove the hypothetical index with `hypopg_reset()`.
5. Commit the transaction, then reset `__neki.shard`.

```sql theme={null}
SET __neki.shard = '<shard-uid>';
BEGIN;
SELECT * FROM hypopg_create_index('CREATE INDEX ON public.orders (user_id)');
EXPLAIN SELECT * FROM public.orders WHERE user_id = 42;
SELECT hypopg_reset();
COMMIT;
RESET __neki.shard;
```

If you do not call `hypopg_reset()`, later sessions on that shard can see the
hypothetical index. Repeat the steps for each shard where the plan matters.
Use plain `EXPLAIN` only. Neki rejects `EXPLAIN ANALYZE` under `__neki.shard`,
and `hypopg` does not apply hypothetical indexes to `EXPLAIN ANALYZE`.

### pg\_stat\_statements

[Enable `pg_stat_statements` on the configuration
profile](extensions.md#enable-an-extension-for-a-profile), then run `CREATE
EXTENSION pg_stat_statements`. The profile change restarts Postgres.

A read of the `pg_stat_statements` view through the router returns the
statistics of one shard only. Neki gives no warning. The router rejects
`pg_stat_statements_reset()` when Neki must plan it.

On the targeted shard, a read returns the statistics of that shard. A call to
`pg_stat_statements_reset()` resets the statistics of that shard. Repeat the
read or the reset for each shard.

```sql theme={null}
SET __neki.shard = '<shard-uid>';
-- Read the statistics of this shard.
SELECT query, calls, total_exec_time, mean_exec_time
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 10;
-- Reset the statistics of this shard.
SELECT pg_stat_statements_reset();
RESET __neki.shard;
```

Client SQL is the SQL that your application sends to the router. Shard SQL is
the SQL that the router builds from the client SQL and sends to a shard. The
view on each shard differs from the view on one Postgres instance:

* The `query` column shows shard SQL, not client SQL. For example, an `IN`
  list becomes `= ANY($2)`.
* When the router splits a client statement, each shard shows only its own
  part. No entry gives the total time of the client statement.
* Work that the router does, for example a join across shards, is not in the
  view.
* The view also shows internal Neki statements. These entries count toward
  `pg_stat_statements.max`.
* A shard that a shard split creates starts with empty statistics.
* A monitoring tool that reads the view through the router, for example
  Datadog or pganalyze, gets the statistics of one shard only.

The internal Neki statements run as Neki roles. To show only the statements
of your role, filter on `userid`:

```sql theme={null}
SELECT query, calls, total_exec_time, mean_exec_time
FROM pg_stat_statements
WHERE userid = current_user::regrole::oid
ORDER BY total_exec_time DESC
LIMIT 10;
```

Some `SET` statements that Neki sends for your session run as your role. These
statements stay in the result. If your application connects with other roles,
filter on those roles.

For statistics of client SQL across all shards, use [Query
Insights](https://planetscale.com/docs/neki/monitoring/query-insights.md). For the parameters and the
columns of the view, see
[pg\_stat\_statements](https://planetscale.com/docs/postgres/extensions/pg_stat_statements.md).

## External Postgres

Connecting an externally managed Postgres source to a Neki migration workflow is not supported during Platform Preview. For the preview, import Postgres data into a Neki-managed, unsharded database instead.

See [Import a Postgres database](https://planetscale.com/docs/neki/imports/postgres.md) for the supported
dump-and-restore path.

## Cross-shard transactions

A cross-shard transaction is a transaction that reaches more than one Postgres
shard. Cross-shard transactions do not provide atomic commit across all shards.
See [Transactions across
shards](https://planetscale.com/docs/neki/transactions.md).

Neki supports a `single` transaction routing mode that rejects an operation
when a second shard would join the transaction. Atomic distributed transaction
mode is not supported.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
