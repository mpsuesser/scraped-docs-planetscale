---
url: https://planetscale.com/docs/neki/extensions
title: "Extensions"
description: ""
access_date: 2026-09-15T23:05:23.430Z
current_date: 2026-09-15T23:05:23.430Z
---

Postgres extensions add data types, functions, operators, background workers, and other capabilities to Postgres. Extensions that do not require a profile-level toggle install in a logical database with `CREATE EXTENSION`.

## Extension availability

Extensions marked **Always enabled** are part of Neki’s managed configuration and cannot be toggled. Other extensions can use either of these workflows:

- A checkbox enables server configuration that the extension needs, such as a preload setting. Changing it creates a configuration-profile change.
- An extension without a checkbox does not require a profile-level toggle. You install it in a logical database with `CREATE EXTENSION` when it is available.

Enabling an extension for a configuration profile and installing its database objects are separate operations. A profile-level change applies to the Postgres instances in shards assigned to that profile. `CREATE EXTENSION` installs objects in the logical database to which you are connected.

## Enable an extension for a profile

You must have permission to update the database, and the branch must be ready.

A configuration-profile change affects every shard assigned to that profile. Confirm the assigned shards before changing an extension or its parameters.

Some profile changes can restart Postgres processes or roll through the instances in the profile. Use the change status to follow the rollout and avoid making dependent application changes before it completes.

The available extension list for a profile is also available from `pscale branch config-profile extensions`. Enable or disable a catalog extension that the profile marks as enablable with `pscale branch config-profile extensions enable` or `pscale branch config-profile extensions disable`.

## Install an extension in a logical database

After any required profile change has completed, connect to the target logical database with its default role. When the extension’s instructions require database installation, run:

```sql
CREATE EXTENSION IF NOT EXISTS extension_name;
```

Replace `extension_name` with the extension you want to install. Repeat the command for each logical database that needs the extension.

To see extensions that are installed in the current logical database:

```sql
SELECT name, default_version, installed_version, comment
FROM pg_available_extensions
WHERE installed_version IS NOT NULL
ORDER BY name;
```

During Platform Preview, do not use `ALTER EXTENSION ... ADD` to attach a user-created table, type, function, sequence, view, or other object to an extension. A shard created or rebuilt later may not recreate that object. Contact PlanetScale Support before using extension-membership DDL.

## Supported extensions

### Always enabled

These extensions are part of Neki’s managed configuration and cannot be disabled.

| Extension | Version | Purpose |
| --- | --- | --- |
| `neki_xxhash` | 0.4 | Hash functions used by Neki distribution |
| `pg_pscale_utils` | — | Handles privileged actions without granting superuser access |
| `pgextwlist` | — | Controls which extensions can be installed |
| `pginsights` | — | Collects per-query execution statistics for [Query Insights](monitoring/query-insights.md) |
| `plpgsql` | 1.0 | Built-in PL/pgSQL procedural language |

### Built-in PostgreSQL extensions

These modules ship with PostgreSQL. They do not need a configuration-profile checkbox. Install each one in the logical databases that need it:

```sql
CREATE EXTENSION IF NOT EXISTS btree_gist;
```

| Extension | Version | Database install | Purpose |
| --- | --- | --- | --- |
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
| [`tcn`](https://www.postgresql.org/docs/current/tcn.html) | 1.0 | `CREATE EXTENSION tcn` | Trigger function that notifies listeners of table changes |
| [`tsm_system_rows`](https://www.postgresql.org/docs/current/tsm-system-rows.html) | 1.0 | `CREATE EXTENSION tsm_system_rows` | `TABLESAMPLE` method `SYSTEM_ROWS` |
| [`tsm_system_time`](https://www.postgresql.org/docs/current/tsm-system-time.html) | 1.0 | `CREATE EXTENSION tsm_system_time` | `TABLESAMPLE` method `SYSTEM_TIME` |
| [`unaccent`](https://www.postgresql.org/docs/current/unaccent.html) | 1.1 | `CREATE EXTENSION unaccent` | Text-search dictionary that strips accents |
| [`uuid-ossp`](https://www.postgresql.org/docs/current/uuid-ossp.html) | 1.1 | `CREATE EXTENSION "uuid-ossp"` | UUID generation functions |

### Community extensions

| Extension | Version | Profile toggle | Database install | Notes |
| --- | --- | --- | --- | --- |
| [`vector`](#pgvector) (pgvector) | 0.8.5 | Enable | `CREATE EXTENSION vector` | Usable on unsharded and sharded Neki with [query-shape limits](#pgvector) |
| [`vectorscale`](#vectorscale) | 0.9.0 | — | `CREATE EXTENSION vectorscale` | Requires `vector`. Enable on the profile to expose DiskANN query parameters. Inherits the [pgvector query-shape limits](#pgvector) |

## Extension-specific caveats

Neki can use an extension without implementing every function, cast, or cross-shard query shape that Postgres accepts on a single node. Prefer the working forms below. A rejected shape often returns `NK013` for an unimplemented input, receive, or user-defined function.

### pgvector

Install [pgvector](https://github.com/pgvector/pgvector) after you enable it on the configuration profile:

```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

Nearest-neighbor search works for `vector`, `halfvec`, `sparsevec`, and binary vectors, including HNSW and IVFFlat indexes. Tenant-scoped and shard-targeted searches work. Cross-shard search works when the distance expression is projected once and the global sort references that projected value:

```sql
SELECT tenant_id, item_id, embedding <-> '[1,0,0]'::vector AS distance
FROM public.vector_items
ORDER BY distance
LIMIT 20;
```

The same alias-based shape works for cosine (`<=>`), inner product (`<#>`), L1, halfvec, sparsevec, Hamming, and Jaccard distance.

Repeating the distance expression in `ORDER BY` fails on sharded Neki:

```sql
-- Rejected: NK013 receive function vector_recv is not implemented
SELECT tenant_id, item_id, embedding <-> '[1,0,0]'::vector AS distance
FROM public.vector_items
ORDER BY embedding <-> '[1,0,0]'::vector
LIMIT 20;
```

Other current limits:

- A prepared parameter declared as `vector` fails with `NK013` because `vector_in` is not implemented. Declare the parameter as `text` and cast `$1::vector` inside the query.
- `ARRAY[...]::vector` and `ARRAY[...]::vector(8)` are rejected. Use a vector literal such as `'[1,0,0]'::vector`.
- `l2_norm(embedding)` is rejected as ambiguous even when the column type is `vector`.
- Global `avg(vector)` and `sum(vector)` fail on sharded tables with `vector_recv`. Tenant-scoped and direct-shard aggregates work.
- Routed `INSERT ... SELECT` of vector values can hit the same `vector_recv` limit. Insert vector literals, or load rows with a query that stays on one shard.
- IVFFlat recall is poor at the default `ivfflat.probes=1`. Raise `probes` when you need higher recall. HNSW recall depends on `hnsw.ef_search`.

### vectorscale

[vectorscale](https://github.com/timescale/pgvectorscale) adds StreamingDiskANN indexes on top of pgvector. Enable and install `vector` first, then:

```sql
CREATE EXTENSION IF NOT EXISTS vectorscale;
```

Tenant-routed and scatter searches that use a StreamingDiskANN index work when they follow the [pgvector `ORDER BY distance` shape](#pgvector). The pgvector parameter-binding, literal-cast, and `vector_recv` limits also apply, including routed `INSERT ... SELECT` of vector values.

Enabling `vectorscale` on the **Extensions** tab exposes these DiskANN query parameters:

| Parameter | Dashboard default | Description |
| --- | --- | --- |
| `diskann.query_search_list_size` | `100` | The size of the search list used in queries. |
| `diskann.query_rescore` | `50` | The number of elements rescored (`0` disables rescoring). |

## Change extension parameters

Parameters associated with an extension appear beneath it after the extension is enabled. Their valid values, defaults, and restart requirements are shown in the dashboard. Updating them creates the same kind of configuration-profile change as other [configuration parameters](cluster-configuration/parameters.md).

## Disable or remove an extension

Disabling a profile-level extension setting does not run `DROP EXTENSION` and does not remove database objects from logical databases. Before disabling it, remove or migrate any database objects and application features that depend on it according to that extension’s instructions.

`DROP EXTENSION` is a separate Postgres operation and can remove dependent objects. Review the objects that depend on an extension before running it.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
