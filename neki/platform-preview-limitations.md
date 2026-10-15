---
url: https://planetscale.com/docs/neki/platform-preview-limitations
title: "Platform Preview Limitations"
description: ""
access_date: 2026-10-15T11:57:20.597Z
current_date: 2026-10-15T11:57:20.597Z
---

Neki is currently in Platform Preview. Platform Preview features are “Beta Features” under the PlanetScale Terms of Service or your applicable agreement with PlanetScale. Accordingly, Neki is subject to the limitations and disclaimers applicable to Beta Features and is not covered by any service level agreement.

During the Platform Preview, Neki’s router does not support the following Postgres features or query shapes. These broad limits can change how an application connects, writes SQL, or manages a shard. The planner can reject more specific query combinations with a `not supported` or `not implemented` error. [Neki error codes](error-codes.md) lists these rejections, the query shapes that trigger them, and what to do instead.

## High availability

Single-node, non-high-availability configurations are not supported during Platform Preview.

## SQL and relation limits

The router rejects the following operations:

- `SELECT ... INTO`.
- `INTERSECT` and `EXCEPT` when Neki must plan a query for an application table. `UNION` is supported. This limit does not apply to statements that Neki forwards unchanged, such as direct-shard statements.
- `COMMIT AND CHAIN` and `ROLLBACK AND CHAIN`.
- Cross-database object references.
- Reading a Postgres inheritance parent without `ONLY` when it has child tables.
- A `search_path` that includes `pg_temp`.

## COPY limits

`COPY` must use the simple query protocol and must be the only statement in the query. Neki rejects `COPY` over the extended query protocol.

The router supports `COPY FROM STDIN` and client `COPY TO STDOUT` (text, CSV, and binary) for unsharded tables, sharded tables, and reference tables. Client `COPY TO STDOUT` also works from a materialized view. For a sharded table, include the single-column shard key in the copied columns. Sharded `COPY FROM` does not support `ON_ERROR`, `REJECT_LIMIT`, or `LOG_VERBOSITY`.

File-based `COPY` supports unsharded tables only. Neki rejects `COPY FROM PROGRAM`, `COPY TO PROGRAM`, `COPY (SELECT ...) TO`, and `COPY FROM` with a `WHERE` clause.

When `COPY FROM` omits an identity column or a column with a sequence-backed or router-only default, supply that column’s values explicitly.

## User-defined text search

Neki supports Postgres’s built-in text-search configurations and dictionaries. During Platform Preview, the router rejects creating a user-defined text-search configuration or dictionary and rejects changes to their definitions. Router-side text-search evaluation cannot use those custom definitions.

## Cluster-level objects

Neki rejects cluster-level objects that depend on storage or code local to one Postgres host. This includes:

- Creating a tablespace or moving a database into a tablespace.
- Creating Postgres large objects.
- Creating a procedural language with a custom handler. Built-in languages and languages installed by a supported extension remain available.
- Loading a shared library with `LOAD`.

## Extensions that need shard targeting

The router rejects the extension calls in this section when Neki must plan them. Each call acts on one Postgres server, or reports the state of one server. To use these calls, target one shard directly:

1. Get the shard UIDs from `__neki.list_shards()`.
2. Set `__neki.shard` to one shard UID. Neki forwards each call to that shard unchanged.
3. Run the call. The result covers the targeted shard only.
4. Repeat steps 2 and 3 for each shard that the result must cover.
5. Reset `__neki.shard` to restore normal routing.

```sql
SELECT uid, name FROM __neki.list_shards();
SET __neki.shard = '<shard-uid>';
-- Run the extension call here.
RESET __neki.shard;
```

See [Targeting one shard directly](shard-targeting-fanout.md#targeting-one-shard-directly).

### pg\_squeeze

The router rejects a `squeeze.squeeze_table()` call when Neki must plan it. The call squeezes the table on the targeted shard only. Repeat it for each shard that holds rows of the table.

### pgrowlocks

The router rejects a `pgrowlocks()` call when Neki must plan it. On the targeted shard, the call shows the locked rows of the table on that shard only. Repeat it for each shard that holds rows of the table.

### pgstattuple

The router rejects the `pgstattuple` functions, for example `pgstattuple()`, when Neki must plan them. On the targeted shard, a call returns the statistics of the rows on that shard only. Repeat it for each shard that holds rows of the table.

To get the statistics of the full table, add the counts and the sizes from all shards. Do not add the percentages. Calculate each percentage again from the added values.

## External Postgres

Connecting an externally managed Postgres source to a Neki migration workflow is not supported during Platform Preview. For the preview, import Postgres data into a Neki-managed, unsharded database instead.

See [Import a Postgres database](imports/postgres.md) for the supported dump-and-restore path.

## Cross-shard transactions

A cross-shard transaction is a transaction that reaches more than one Postgres shard. Cross-shard transactions do not provide atomic commit across all shards. See [Transactions across shards](transactions.md).

Neki supports a `single` transaction routing mode that rejects an operation when a second shard would join the transaction. Atomic distributed transaction mode is not supported.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
