---
url: https://planetscale.com/docs/postgres/search
title: "Search"
description: ""
access_date: 2026-09-17T17:35:52.931Z
current_date: 2026-09-17T17:35:52.931Z
---

TIN (**T** ext **IN** dex) brings blazing-fast full-text search to PlanetScale Postgres. Enabling the `tin` extension adds an inverted index type built for search, BM25 ranking, and the **[TINQL](search/tinql.md)** query language.

For local development and CI, use [Lead](https://github.com/planetscale/lead), a TIN-compatible Postgres extension for testing your application’s search queries on small datasets. See [Local development and CI](search/get-started.md#local-development-and-ci) for setup instructions. Lead is intentionally unsuitable for production workloads.

## A search engine inside Postgres

A TIN index is a normal Postgres index, so searches see a consistent snapshot while writes continue. You also get ranking, highlighting, exact counts, and a query language that can express phrases, proximity, and spans.

TIN offers richer retrieval features and drastically better performance than typical Postgres search extensions and external services.

### Compared with other Postgres search

[TINQL](search/tinql.md) is a broader retrieval language than `tsquery` or `pg_textsearch`, and TIN is substantially faster than `pg_textsearch` and ParadeDB `pg_search` on the count and top-k workloads those engines can run.

| Capability | TIN | Postgres FTS | ParadeDB `pg_search` | `pg_textsearch` |
| --- | --- | --- | --- | --- |
| Available on PlanetScale | ✅ | ✅ | ❌ | ❌ |
| BM25 ranking | ✅ | ❌ | ✅ | ✅ |
| Top-k retrieval | ✅ | Limited | ✅ | ✅ |
| `AND` / `OR` / `NOT` | ✅ | ✅ | ✅ | ❌ |
| Phrases with word gaps | ✅ | Limited | ✅ | ❌ |
| Proximity and span queries | ✅ | ❌ | Limited | ❌ |
| Fuzzy terms, wildcards, boosts | ✅ | Limited | ✅ | ❌ |
| Query language | TINQL | `tsquery` | Query API | Term string |
| Highlighting | ✅ | ✅ | ✅ | ❌ |
| Indexes `text` directly | ✅ | ❌ | ✅ | ✅ |
| Stemming | ❌ | ✅ | ✅ | ✅ |

An external engine such as Elasticsearch has a similar feature set, but requires a second cluster and a sync path with Postgres.

### Key features

- **[The `==>` operator](search/reference/operator.md).** `WHERE body ==> '…'` searches a TIN-indexed column with a TINQL string. `==>` is boolean: the row matches if the text satisfies the query.
- **[TIN index](search/reference/indexes.md).** Create a TIN index with `CREATE INDEX ... USING tin` on a `text` column.
- **[BM25 ranking with top-k retrieval](search/scoring.md).** `tin.score(ctid)` returns the BM25 relevance of each matching row, and `ORDER BY tin.score(ctid) DESC LIMIT 10` returns the 10 best documents in score order. The same row gets the same score however the query is executed. `tin.max_score(ctid)` gives a denominator for normalized relevance, and `k1` and `b` can be tuned per index or per query.
- **[The TINQL query language](search/tinql.md).** One query string expresses phrases with word gaps, ordered and unordered proximity (`THEN/5`, `NEAR/5`), span relations (`ENCLOSES`, `OVERLAPPING`, `BEFORE`, `AFTER`), positional filters (`IN FIRST 100 WORDS`), wildcards, fuzzy terms, regular expressions, term ranges, `AT LEAST 2 OF`, and boosts. Every expression produces spans, so the operators compose freely.
- **Exact counts.** `count(*)` with `WHERE body ==> '…'` is answered from the index and stays exact under concurrent writes, `VACUUM`, and on read replicas.
- **[Highlighting](search/highlighting.md).** `tin.highlight` adds configurable markers around the text that produced the match. When highlighted, a match on `pineapple` returns `'<b>pineapple</b>'`. `tin.highlight_ansi` does the same for terminals.
- **[Cross-column scoring](search/reference/sql-shapes.md#several-tin-indexed-columns).** Search several TIN-indexed columns in one query, and `tin.score(ctid)` combines relevance across them.
- **[Configurable tokenization](search/reference/indexes.md#index-options-with).** The default tokenizer runs on indexed columns and queries, splitting text on Unicode word boundaries, folding case and accents, and indexes emoji as terms.
- **[Parallel build and parallel query](search/operations.md#parallel-query).** Index builds use parallel workers, and based on cost, queries and `count(*)` can run in parallel too.

### TIN example

Create a table, insert some rows, index the text column, and query it:

If `CREATE EXTENSION` fails with `permission denied to create extension "tin"` and `Must be superuser to create this extension.`, your cluster needs an update before it can install TIN. Go to the **Clusters** page for your branch, find the “Cluster update available” indicator, and [update your cluster](cluster-configuration/updates.md). After the update completes, run `CREATE EXTENSION` again.

```sql
CREATE EXTENSION IF NOT EXISTS tin;

CREATE TABLE posts (
  id   bigint PRIMARY KEY,
  body text NOT NULL
);

INSERT INTO posts (id, body) VALUES
  (1, 'Fuji apple slices with citrus and melon make a bright fruit salad'),
  (2, 'Fuji apple, citrus, and melon for lunch'),
  (3, 'Peel the fuji apple and toss it with citrus and melon'),
  (4, 'Citrus and melon salad with fresh mint'),
  (5, 'Grape tasting notes from the orchard');

CREATE INDEX posts_body_tin ON posts USING tin (body);

SELECT id,
       tin.score(ctid) AS score,
       tin.highlight(body)
FROM posts
WHERE body ==> '(citrus NEAR/5 melon) AND "fuji apple" AND NOT peel'
ORDER BY score DESC
LIMIT 10;
```

The query matches rows 1 and 2. Each contains the phrase `fuji apple`, has `citrus` within five words of `melon`, and excludes `peel`. Row 3 has all three terms but is excluded by `AND NOT peel`, and rows 4 and 5 lack the phrase. `tin.score(ctid)` ranks the matches by BM25 relevance, and `tin.highlight(body)` adds `<b>` / `</b>` tags around the text that produced the match.

## Hybrid search with pgvector

Hybrid search requires a keyword (lexical) search and a meaning (semantic) search over the same documents, then combining the two ranked lists. TIN does the lexical half.

Semantic search needs an embedding model outside Postgres and a vector index on the same table. The model embeds each document when you write it, and embeds the user’s query at search time. [pgvector](extensions/pgvector.md) stores those vectors and returns the nearest rows.

```sql
CREATE TABLE posts (
  id bigint PRIMARY KEY,
  body text NOT NULL,
  embedding vector(1536) NOT NULL
);

CREATE INDEX posts_body_tin ON posts USING tin (body);
CREATE INDEX posts_embedding ON posts USING hnsw (embedding vector_cosine_ops);

-- Lexical: words and phrases in the query
SELECT id
FROM posts
WHERE body ==> '"fuji apple"'
ORDER BY tin.score(ctid) DESC
LIMIT 10;

-- Semantic: embedding of that same query, from the same model that embedded \`body\`
SELECT id
FROM posts
ORDER BY embedding <=> $query_embedding
LIMIT 10;
```

You would then combine these results in your application, keeping every `id` from either query, and treating an `id` that appears in both as a stronger match.

## Next steps

- [Get started](search/get-started.md) — install the extension, index a column, run your first query
- [Scoring](search/scoring.md) — BM25 relevance and top-k retrieval
- [Highlighting](search/highlighting.md) — mark the text that matched
- [TINQL](search/tinql.md) — the query language
- [Operational guidance](search/operations.md) — vacuum settings for tables with churn
- [Reference](search/reference/indexes.md) — index options and the SQL API

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
