---
url: https://planetscale.com/docs/neki/platform-preview-limitations
title: "Platform Preview Limitations"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Neki is currently in Platform Preview. Platform Preview features are “Beta Features” under the PlanetScale Terms of Service or your applicable agreement with PlanetScale. Accordingly, Neki is subject to the limitations and disclaimers applicable to Beta Features and is not covered by any service level agreement.

During the Platform Preview, Neki’s router does not support the following Postgres features or query shapes. These broad limits can change how an application connects, writes SQL, or manages a shard. The planner can reject more specific query combinations with a `not supported` or `not implemented` error.

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

The router supports `COPY FROM STDIN` for unsharded tables, sharded tables, and reference tables. For a sharded table, include the single-column shard key in the copied columns. Sharded `COPY FROM` does not support `ON_ERROR`, `REJECT_LIMIT`, or `LOG_VERBOSITY`.

`COPY TO` supports unsharded tables only. File-based `COPY` supports unsharded tables only. Neki rejects `COPY FROM PROGRAM`, `COPY TO PROGRAM`, `COPY (SELECT ...) TO`, and `COPY FROM` with a `WHERE` clause.

When `COPY FROM` omits an identity column or a column with a sequence-backed or router-only default, supply that column’s values explicitly.

## User-defined text search

Neki supports Postgres’s built-in text-search configurations and dictionaries. During Platform Preview, the router rejects creating a user-defined text-search configuration or dictionary and rejects changes to their definitions. Router-side text-search evaluation cannot use those custom definitions.

## Cluster-level objects

Neki rejects cluster-level objects that depend on storage or code local to one Postgres host. This includes:

- Creating a tablespace or moving a database into a tablespace.
- Creating Postgres large objects.
- Creating a procedural language with a custom handler. Built-in languages and languages installed by a supported extension remain available.
- Loading a shared library with `LOAD`.

## External Postgres

Connecting an externally managed Postgres source to a Neki migration workflow is not supported during Platform Preview. For the preview, import Postgres data into a Neki-managed, unsharded database instead.

See [Import a Postgres database](imports/postgres.md) for the supported dump-and-restore path.

## Cross-shard transactions

A cross-shard transaction is a transaction that reaches more than one Postgres shard. Cross-shard transactions do not provide atomic commit across all shards. See [Transactions across shards](query-planning.md#transactions-across-shards).

Neki supports a `single` transaction routing mode that rejects an operation when a second shard would join the transaction. Atomic distributed transaction mode is not supported.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
