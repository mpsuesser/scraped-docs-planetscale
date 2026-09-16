---
url: https://planetscale.com/docs/postgres/search/scoring
title: "Scoring"
description: ""
access_date: 2026-09-16T16:23:24.602Z
current_date: 2026-09-16T16:23:24.602Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Scoring

> BM25 relevance with `tin.score`, `tin.full_score`, and `tin.max_score`.

**Platform availability:** Postgres only

`tin.score(ctid)` returns a BM25 score over the rows a TIN scan matched.

[The `==>` operator](reference/operator.md) performs a TINQL query against a TIN index to find matches, and `tin.score` assigns each matching row a number. Scoring does not sort on its own, so write `ORDER BY tin.score(ctid) DESC` to sort from closest to farthest matches.

## `tin.score(ctid [, dense_ratio [, k1 [, b [, term_add [, term_replace]]]]])`

`tin.score(tid, real, real, real, text[], text[]) → real`

| Argument       | Type     | Default  | Notes                                                                                                                  |
| -------------- | -------- | -------- | ---------------------------------------------------------------------------------------------------------------------- |
| `ctid`         | `tid`    | required | Heap tuple identifier for the current row (`ctid`).                                                                    |
| `dense_ratio`  | `real`   | `0.10`   | Fraction of the document count at which a term is dense and left out of the score. A value above `1` disables elision. |
| `k1`           | `real`   | `NULL`   | Runtime BM25 `k1` override. `NULL` inherits the index option. Domain `[0.0..10000.0]`.                                 |
| `b`            | `real`   | `NULL`   | Runtime BM25 `b` override. `NULL` inherits the index option. Domain `[0.0..1.0]`.                                      |
| `term_add`     | `text[]` | `NULL`   | Terms added to the scored set. Each element is analyzed with the index tokenizer and pinned so elision cannot drop it. |
| `term_replace` | `text[]` | `NULL`   | Terms that become the entire scored set, analyzed with the index tokenizer. Cannot be combined with `term_add`.        |

Returns BM25 relevance for the current row. Requires a TIN index scan in the same query (`col ==> …`). Outside that context, including DML `RETURNING` without a scan, the call raises *requires a tin index scan and cannot be used in this query context*. When the query has multiple `==>` predicates on TIN-indexed columns, the score combines relevance across those fields. Under `FOR UPDATE` / `FOR SHARE`, scores may be `NULL` on concurrently updated rows.

**Dense-term elision.** A term whose document frequency reaches `dense_ratio × N`, where `N` is the document count, is dense, and dense terms are left out of the BM25 sum unless the query boosts them explicitly (`^1.0` counts). A common term carries almost no ranking signal and is expensive to score. At the default ratio, a term found in 10% or more of the documents contributes nothing. A matching row that contains no scored term returns exactly `0.0`. Matching itself is unchanged. On a table of only a few rows, every term is dense, and every score is `0.0`. `tin.full_score` scores every term.

```sql theme={null}
SELECT id, tin.score(ctid) AS score, body
FROM posts
WHERE body ==> 'apple OR grape'
ORDER BY score DESC
LIMIT 10;

-- Override BM25 parameters for this query only (does not change the index)
SELECT id, tin.score(ctid, k1 => 3.2, b => 0.2) AS score, body
FROM posts
WHERE body ==> 'apple OR grape'
ORDER BY score DESC
LIMIT 10;

-- Score terms found in up to a quarter of the documents
SELECT id, tin.score(ctid, dense_ratio => 0.25) AS score, body
FROM posts
WHERE body ==> 'apple OR grape'
ORDER BY score DESC
LIMIT 10;
```

Index defaults come from `WITH (k1 = …, b = …)` on the TIN index (or `1.2` / `0.75` when unset). Runtime overrides must be statement-constant and share the same domains as the index options. `k1` and `b` may differ between `tin.score` calls on the same relation. `dense_ratio`, `term_add`, and `term_replace` must be written identically in every call.

## `tin.full_score(ctid [, k1, b])`

`tin.full_score(tid) → real`\
`tin.full_score(tid, real, real) → real`

| Argument | Type   | Notes                                                         |
| -------- | ------ | ------------------------------------------------------------- |
| `ctid`   | `tid`  | Heap tuple identifier for the current row (`ctid`).           |
| `k1`     | `real` | Runtime BM25 `k1` override. `NULL` inherits the index option. |
| `b`      | `real` | Runtime BM25 `b` override. `NULL` inherits the index option.  |

Returns BM25 relevance with every query term kept in the sum. Dense-term elision and `score_stop_words` do not apply, so common terms contribute to the ranking and cost more to score on a large corpus. It has the same scan requirement as `tin.score`, and you can't combine the two in a single scanned relation.

```sql theme={null}
SELECT id, tin.full_score(ctid) AS score, body
FROM posts
WHERE body ==> 'the AND midnight'
ORDER BY score DESC
LIMIT 10;
```

## `tin.max_score(ctid)`

`tin.max_score(tid) → real`

| Argument | Type  | Notes                                               |
| -------- | ----- | --------------------------------------------------- |
| `ctid`   | `tid` | Heap tuple identifier for the current row (`ctid`). |

Returns the highest BM25 score over the query’s visible matches. The value is constant for the scan, which makes it the natural denominator for normalized relevance. Like `tin.score`, it must be used alongside a TIN index scan, or the query errors. It follows whichever of `tin.score` and `tin.full_score` the query uses.

```sql theme={null}
SELECT id,
       tin.score(ctid) AS score,
       tin.score(ctid) / tin.max_score(ctid) AS relative
FROM posts
WHERE body ==> 'quick AND fox'
ORDER BY score DESC
LIMIT 10;
```

## `tin.score_inspect(index, query [, dense_ratio [, term_add [, term_replace]]])`

`tin.score_inspect(regclass, text, real, text[], text[]) → setof (term text, weight real)`

| Argument       | Type       | Default  | Notes                                              |
| -------------- | ---------- | -------- | -------------------------------------------------- |
| `index`        | `regclass` | required | The TIN index whose statistics and options to use. |
| `query`        | `text`     | required | The TINQL query to inspect.                        |
| `dense_ratio`  | `real`     | `0.10`   | Same meaning as in `tin.score`.                    |
| `term_add`     | `text[]`   | `NULL`   | Same meaning as in `tin.score`.                    |
| `term_replace` | `text[]`   | `NULL`   | Same meaning as in `tin.score`.                    |

Returns one row per term that a `tin.score` scan with the same arguments would score, with the term's accumulated boost weight. It runs the same steps as `tin.score` without scanning anything: query terms, minus `score_stop_words`, minus dense terms, plus terms pinned by a boost or by `term_add`. Use it to see what `dense_ratio` drops on your corpus and to check that a `score_stop_words` entry matches a stored term. It requires `SELECT` on the index's table.

```sql theme={null}
SELECT * FROM tin.score_inspect('posts_body_tin', 'common^1.0 OR mid OR rare', 0.25)
ORDER BY term;
--   term  | weight
--  common |      1
--  rare   |      1
```

`mid` is dense at that ratio, so it is left out. `common` is denser still, but the explicit `^1.0` keeps it in.

## Visibility

`tin.score` scores only rows your query can see. A row that is not visible to your snapshot, whether it was deleted, replaced by an update, or inserted by a transaction that has not committed, is never returned and never receives a score. It also never influences which rows make a `LIMIT k` cut.

The corpus statistics behind BM25 work differently. The document count, the number of documents containing each term, and the average document length come from the index, and they include every document the index still stores. A deleted or updated row keeps its entry in the index until `VACUUM` marks it dead and background maintenance rewrites the segment that holds it, or until you `REINDEX`. Until then, the deleted rows still count in the statistics, and the rows you do see are scored as if the deleted rows were still present. On a table with heavy update or delete churn, keep autovacuum aggressive so that maintenance can drop dead entries promptly. See [Operational guidance](operations.md).

Scores are comparable within one query. They shift over time as the corpus grows and as maintenance reshapes it.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
