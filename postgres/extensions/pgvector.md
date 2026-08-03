---
url: https://planetscale.com/docs/postgres/extensions/pgvector
title: "Pgvector"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Extensions: pgvector

> Open-source vector similarity search for Postgres, Store embeddings and run nearest-neighbor queries

**Platform availability:** [Vitess](../../vitess/vectors.md) and Postgres

## Overview

[pgvector](https://github.com/pgvector/pgvector) adds a `vector` column type, distance operators, and approximate nearest-neighbor indexes (HNSW and IVFFlat) to PostgreSQL. Use it to store embeddings alongside relational data and query by similarity.

PlanetScale Postgres also supports [pgvectorscale](https://github.com/timescale/pgvectorscale), a companion extension for higher-performance approximate search. See the [supported extensions](../extensions.md#supported-community-extensions) table for available versions.

## Enabling pgvector

pgvector does not require a cluster restart. Install it with a role that has superuser privileges:

```sql theme={null}
CREATE EXTENSION IF NOT EXISTS vector;
```

Confirm the extension is installed:

```sql theme={null}
SELECT extname, extversion
FROM pg_extension
WHERE extname = 'vector';
```

## Usage

Create a table with a vector column, insert embeddings, and query by distance:

```sql theme={null}
CREATE TABLE items (
  id bigserial PRIMARY KEY,
  content text,
  embedding vector(3)
);

INSERT INTO items (content, embedding) VALUES
  ('red', '[1,0,0]'),
  ('green', '[0,1,0]'),
  ('blue', '[0,0,1]');

-- Nearest neighbors by L2 distance
SELECT id, content
FROM items
ORDER BY embedding <-> '[1,0.2,0]'
LIMIT 5;
```

Common distance operators:

| Operator | Meaning                |
| -------- | ---------------------- |
| `<->`    | L2 distance            |
| `<#>`    | Negative inner product |
| `<=>`    | Cosine distance        |

Add an HNSW index when you need approximate search at scale:

```sql theme={null}
CREATE INDEX ON items USING hnsw (embedding vector_l2_ops);
```

## External documentation

For indexing options, half-precision and sparse vectors, and language clients, see the [official pgvector documentation](https://github.com/pgvector/pgvector).
