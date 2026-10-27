---
url: https://planetscale.com/docs/neki/extensions
title: "Extensions"
description: ""
access_date: 2026-10-27T16:16:59.369Z
current_date: 2026-10-27T16:16:59.369Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Postgres extensions

> Built-in and community Postgres extensions available on Neki.

Postgres extensions add data types, functions, operators, background workers,
and other capabilities to Postgres. Extensions that do not require a
profile-level toggle install in a logical database with `CREATE EXTENSION`.

## Extension availability

Extensions marked **Always enabled** are part of Neki's managed configuration
and cannot be toggled. Other extensions can use either of these workflows:

* A checkbox enables server configuration that the extension needs, such as a
  preload setting. Changing it creates a configuration-profile change.
* An extension without a checkbox does not require a profile-level toggle. You
  install it in a logical database with `CREATE EXTENSION` when it is available.

Enabling an extension for a configuration profile and installing its database
objects are separate operations. A profile-level change applies to the
Postgres instances in shards assigned to that profile. `CREATE EXTENSION`
installs objects in the logical database to which you are connected.

## Enable an extension for a profile

You must have permission to update the database, and the branch must be ready.

**Step 1**

From the PlanetScale dashboard, select your Neki database and branch.

**Step 2**

Open **Clusters** and select the configuration profile used by the target shards.

**Step 3**

Select the **Extensions** tab.

**Step 4**

Enable the extension. If the extension exposes parameters, review and update them.

**Step 5**

Select **Apply now** for any parameter changes.

**Step 6**

Open **Changes** and wait for the configuration change to finish before using the extension.

> [!WARNING]
>
> A configuration-profile change affects every shard assigned to that profile.
> Confirm the assigned shards before changing an extension or its parameters.

Some profile changes can restart Postgres processes or roll through the
instances in the profile. Use the change status to follow the rollout and avoid
making dependent application changes before it completes.

The available extension list for a profile is also available from
`pscale branch config-profile extensions`. Enable or disable a catalog
extension that the profile marks as enablable with
`pscale branch config-profile extensions enable` or
`pscale branch config-profile extensions disable`.

## Install an extension in a logical database

After any required profile change has completed, connect to the target logical
database with its default role. When the extension's instructions require
database installation, run:

```sql theme={null}
CREATE EXTENSION IF NOT EXISTS extension_name;
```

Replace `extension_name` with the extension you want to install. Repeat
the command for each logical database that needs the extension.

To see extensions that are installed in the current logical database:

```sql theme={null}
SELECT name, default_version, installed_version, comment
FROM pg_available_extensions
WHERE installed_version IS NOT NULL
ORDER BY name;
```

> [!WARNING]
>
> During Platform Preview, do not use `ALTER EXTENSION ... ADD` to attach a
> user-created table, type, function, sequence, view, or other object to an
> extension. A shard created or rebuilt later may not recreate that object.
> Contact PlanetScale Support before using extension-membership DDL.

## Supported extensions

### Always enabled

These extensions are part of Neki's managed configuration and cannot be
disabled.

| Extension | Version | Purpose |
| - | - | - |
| `neki_xxhash` | 0.4 | Hash functions used by Neki distribution |
| `pg_pscale_utils` | — | Handles privileged actions without granting superuser access |
| `pgextwlist` | — | Controls which extensions can be installed |
| `pginsights` | — | Collects per-query execution statistics for [Query Insights](https://planetscale.com/docs/neki/monitoring/query-insights.md) |
| `plpgsql` | 1.0 | Built-in PL/pgSQL procedural language |

### Built-in PostgreSQL extensions

These modules ship with PostgreSQL. They do not need a configuration-profile
checkbox. Install each one in the logical databases that need it:

```sql theme={null}
CREATE EXTENSION IF NOT EXISTS btree_gist;
```

| Extension | Version | Database install | Purpose |
| - | - | - | - |
| [`bloom`](https://www.postgresql.org/docs/current/bloom.html) | 1.0 | `CREATE EXTENSION bloom` | Bloom filter index access method |
| [`btree_gin`](https://www.postgresql.org/docs/current/btree-gin.html) | 1.3 | `CREATE EXTENSION btree_gin` | B-tree equivalent operator classes for GIN |
| [`btree_gist`](https://www.postgresql.org/docs/current/btree-gist.html) | 1.8 | `CREATE EXTENSION btree_gist` | B-tree equivalent operator classes for GiST |
| [`citext`](https://www.postgresql.org/docs/current/citext.html) | 1.8 | `CREATE EXTENSION citext` | Case-insensitive text type |
| [`cube`](https://www.postgresql.org/docs/current/cube.html) | 1.5 | `CREATE EXTENSION cube` | Multidimensional cube type |
| [`fuzzystrmatch`](https://www.postgresql.org/docs/current/fuzzystrmatch.html) | 1.2 | `CREATE EXTENSION fuzzystrmatch` | String similarity and distance functions |
| [`hstore`](https://www.postgresql.org/docs/current/hstore.html) | 1.8 | `CREATE EXTENSION hstore` | Key/value pairs stored in a single value |
| [`insert_username`](https://www.postgresql.org/docs/current/contrib-spi.html#CONTRIB-SPI-INSERT-USERNAME) | 1.0 | `CREATE EXTENSION insert_username` | Trigger functions that record who changed a table |
| [`intarray`](https://www.postgresql.org/docs/current/intarray.html) | 1.5 | `CREATE EXTENSION intarray` | Functions and operators for integer arrays |
| [`ltree`](https://www.postgresql.org/docs/current/ltree.html) | 1.3 | `CREATE EXTENSION ltree` | Labels in a hierarchical tree |
| [`moddatetime`](https://www.postgresql.org/docs/current/contrib-spi.html#CONTRIB-SPI-MODDATETIME) | 1.0 | `CREATE EXTENSION moddatetime` | Trigger functions that record last modification time |
| [`pg_trgm`](https://www.postgresql.org/docs/current/pgtrgm.html) | 1.6 | `CREATE EXTENSION pg_trgm` | Trigram text similarity |
| [`pgcrypto`](https://www.postgresql.org/docs/current/pgcrypto.html) | 1.4 | `CREATE EXTENSION pgcrypto` | Cryptographic functions |
| [`pgrowlocks`](https://www.postgresql.org/docs/current/pgrowlocks.html) | 1.2 | `CREATE EXTENSION pgrowlocks` | Row locks of a table. [Requires shard targeting](https://planetscale.com/docs/neki/platform-preview-limitations.md#pgrowlocks) |
| [`pgstattuple`](https://www.postgresql.org/docs/current/pgstattuple.html) | 1.5 | `CREATE EXTENSION pgstattuple` | Tuple and free-space statistics. [Requires shard targeting](https://planetscale.com/docs/neki/platform-preview-limitations.md#pgstattuple) |
| [`tcn`](https://www.postgresql.org/docs/current/tcn.html) | 1.0 | `CREATE EXTENSION tcn` | Trigger function that notifies listeners of table changes |
| [`tsm_system_rows`](https://www.postgresql.org/docs/current/tsm-system-rows.html) | 1.0 | `CREATE EXTENSION tsm_system_rows` | `TABLESAMPLE` method `SYSTEM_ROWS` |
| [`tsm_system_time`](https://www.postgresql.org/docs/current/tsm-system-time.html) | 1.0 | `CREATE EXTENSION tsm_system_time` | `TABLESAMPLE` method `SYSTEM_TIME` |
| [`unaccent`](https://www.postgresql.org/docs/current/unaccent.html) | 1.1 | `CREATE EXTENSION unaccent` | Text-search dictionary that strips accents |
| [`uuid-ossp`](https://www.postgresql.org/docs/current/uuid-ossp.html) | 1.1 | `CREATE EXTENSION "uuid-ossp"` | UUID generation functions |

### Community extensions

| Extension | Version | Profile toggle | Database install | Notes |
| - | - | - | - | - |
| [`hypopg`](https://github.com/HypoPG/hypopg) | 1.4.3 | — | `CREATE EXTENSION hypopg` | Hypothetical indexes for `EXPLAIN`. [Requires shard targeting](https://planetscale.com/docs/neki/platform-preview-limitations.md#hypopg) |
| [`pg_stat_statements`](https://www.postgresql.org/docs/current/pgstatstatements.html) | 1.12 | Enable | `CREATE EXTENSION pg_stat_statements` | Execution statistics for each statement. The profile change restarts Postgres. [Requires shard targeting](https://planetscale.com/docs/neki/platform-preview-limitations.md#pg_stat_statements) |
| [`vector`](#pgvector) (pgvector) | 0.8.5 | Enable | `CREATE EXTENSION vector` | Usable on unsharded and sharded Neki with [query-shape limits](#pgvector) |
| [`vectorscale`](#vectorscale) | 0.9.0 | — | `CREATE EXTENSION vectorscale` | Requires `vector`. Enable on the profile to expose DiskANN query parameters. Inherits the [pgvector query-shape limits](#pgvector) |

## Extension-specific caveats

Neki can use an extension without implementing every function, cast, or
cross-shard query shape that Postgres accepts on a single node. Prefer the
working forms below. A rejected shape often returns `NK013` for an
unimplemented input, receive, or user-defined function.

### pgvector

Install [pgvector](https://github.com/pgvector/pgvector) after you enable it
on the configuration profile:

```sql theme={null}
CREATE EXTENSION IF NOT EXISTS vector;
```

Nearest-neighbor search works for `vector`, `halfvec`, `sparsevec`, and binary
vectors, including HNSW and IVFFlat indexes. Tenant-scoped, shard-targeted, and
cross-shard searches work. You can project the distance and order by the alias,
or repeat the distance expression in `ORDER BY`:

```sql theme={null}
SELECT tenant_id, item_id, embedding <-> '[1,0,0]'::vector AS distance
FROM public.vector_items
ORDER BY distance
LIMIT 20;
```

Repeating the operator in `ORDER BY`, and ordering by an alias, both work for
cosine (`<=>`), inner product (`<#>`), and L1 (`<+>`) on `vector`, `halfvec`,
and `sparsevec`. Hamming (`<~>`) and Jaccard (`<%>`) work the same way on
`bit`.

These also work on a sharded table:

* A prepared parameter declared as `vector`, and a `text` parameter cast with
  `$1::vector`.
* `ARRAY[1,0,0]::vector` and a vector literal such as `'[1,0,0]'::vector`.
  Cast the literal when the distance is in the select list. An uncast
  `embedding <-> '[1,0,0]'` there fails with
  `operator does not exist: vector <-> text`, for every distance operator.
  The uncast form resolves in `ORDER BY` and `WHERE`.
* Global `avg(vector)` and `sum(vector)`. The router scatters and combines the
  aggregate. Tenant-scoped and direct-shard aggregates work too.
* `INSERT ... SELECT` of vector values, including a statement that reads more
  than one shard.

`l2_norm(embedding)` is ambiguous (`42725`) even when the column type is
`vector`. IVFFlat recall is poor at the default `ivfflat.probes=1`. Raise
`probes` when you need higher recall. HNSW recall depends on `hnsw.ef_search`.

### vectorscale

[vectorscale](https://github.com/timescale/pgvectorscale) adds
StreamingDiskANN indexes on top of pgvector. Enable and install `vector`
first, then:

```sql theme={null}
CREATE EXTENSION IF NOT EXISTS vectorscale;
```

Tenant-routed and scatter searches that use a StreamingDiskANN index work when
they follow the [pgvector `ORDER BY distance` shape](#pgvector).

Enabling `vectorscale` on the **Extensions** tab exposes these DiskANN query parameters:

| Parameter | Dashboard default | Description |
| - | - | - |
| `diskann.query_search_list_size` | `100` | The size of the search list used in queries. |
| `diskann.query_rescore` | `50` | The number of elements rescored (`0` disables rescoring). |

### auto\_explain

[`auto_explain`](https://www.postgresql.org/docs/current/auto-explain.html)
logs the plan of a slow statement. On Neki, each shard runs its own copy of
`auto_explain`. Thus the log shows the plans of shards, not the plan of the
statement that your application sent.

This section uses two terms:

* **Client SQL** is the SQL that your application sends to the router.
* **Shard SQL** is the SQL that the router builds from the client SQL and
  sends to one shard.

#### Set the parameters

Set the `auto_explain.*` parameters on the **Extensions** tab of the
configuration profile. `SET auto_explain.*` in a session fails with
`permission denied to set parameter`. The shards of a profile do not apply a
parameter change at the same time.

#### Differences from one Postgres instance

`auto_explain` compares the executor time of a statement with
`auto_explain.log_min_duration`. The executor time does not include parse
time, plan time, or idle time between fetches. On Neki, this check has these
results:

1. Each shard logs only its own part of a client statement, under the shard
   SQL. The client SQL is not in the log.
2. The threshold applies to each shard statement, not to the client statement.
   For example, a client statement waits 3 s for 10 shard statements of
   300 ms each. With `log_min_duration = 1000`, no shard logs a plan.
3. Router work is never in a shard plan. Examples are a join across shards, a
   sort, and an aggregate that the router does.
4. The log also contains plans of internal Neki statements. With
   `log_min_duration = 0`, a test cluster logged about 1,000 plans in
   2 minutes.

#### Find a logged plan

Each shard writes its plans to its own Postgres log. To read the plans, open
the [Logs](https://planetscale.com/docs/neki/monitoring/logs.md) page and select the shard. The client SQL is
not in the log. Thus, search for the name of a table in the statement.

The Logs page shows a plan at the `INFO` level. A plan can show at a different
level when its text contains a level name, for example `error` in a `WHERE`
clause. To see all plans, do not select a level.

A replica also logs the plans of the statements that it runs. By default, the
Logs page shows only shard primaries. To see the plans of a replica, select
the replica in **Servers**.

#### When to use auto\_explain

| Scenario | Result with `auto_explain` | What to do instead |
| - | - | - |
| A query is sometimes slow | Works when one shard statement is slow. That shard logs its plan under the shard SQL. | Use the log. Search for the plan by table name. |
| Find slow queries in production | Partial. A statement is missed when its time is spread over shards or spent in the router. | Use [Query Insights](https://planetscale.com/docs/neki/monitoring/query-insights.md) to find slow client statements. Then use `EXPLAIN`, as in the next row. |
| A slow query across shards | Not covered. Router work is not logged. | [`EXPLAIN (NEKI_PLAN, ANALYZE)`](query-planning.md#explain-neki_plan) shows the router plan with run-time numbers. [`EXPLAIN (NEKI_PG_PLAN)`](query-planning.md#explain-neki_pg_plan) shows the router plan with the Postgres plan of each shard. |
| A query for one tenant | Works. The shard that owns the tenant logs one plan. | To get the plan by hand, [target that shard](https://planetscale.com/docs/neki/shard-targeting-fanout.md#targeting-one-shard-directly) with `__neki.shard`. Then run a plain `EXPLAIN`. Neki rejects `EXPLAIN ANALYZE`, `NEKI_PLAN`, and `NEKI_PG_PLAN` under `__neki.shard`. |

[EXPLAIN reference](https://planetscale.com/docs/neki/explain-reference.md) shows the full output of
`NEKI_PLAN` and `NEKI_PG_PLAN`.

## Change extension parameters

Parameters associated with an extension appear beneath it after the extension
is enabled. Their valid values, defaults, and restart requirements are shown in
the dashboard. Updating them creates the same kind of configuration-profile
change as other [configuration parameters](https://planetscale.com/docs/neki/cluster-configuration/parameters.md).

## Disable or remove an extension

Disabling a profile-level extension setting does not run `DROP EXTENSION` and
does not remove database objects from logical databases. Before disabling it,
remove or migrate any database objects and application features that depend on
it according to that extension's instructions.

`DROP EXTENSION` is a separate Postgres operation and can remove dependent
objects. Review the objects that depend on an extension before running it.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
