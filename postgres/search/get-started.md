---
url: https://planetscale.com/docs/postgres/search/get-started
title: "Get Started"
description: ""
access_date: 2026-09-17T17:35:52.931Z
current_date: 2026-09-17T17:35:52.931Z
---

## Install the extension

On PlanetScale, enable TIN with the SQL below. For a local or CI database, first [set up Lead](#local-development-and-ci).

The database encoding must be `UTF8` or `SQL_ASCII`. `CREATE EXTENSION tin` refuses other encodings (for example, `LATIN1`).

```sql
CREATE EXTENSION IF NOT EXISTS tin;
```

If `CREATE EXTENSION` fails with `permission denied to create extension "tin"` and `Must be superuser to create this extension.`, your cluster needs an update before it can install TIN. Go to the **Clusters** page for your branch, find the “Cluster update available” indicator, and [update your cluster](../cluster-configuration/updates.md). After the update completes, run `CREATE EXTENSION` again.

## Create a table and index

The example below creates a table, inserts rows, and creates a TIN index on the `text` column:

```sql
CREATE TABLE posts (
  id bigint PRIMARY KEY,
  category text NOT NULL,
  body text NOT NULL
);

INSERT INTO posts (id, category, body) VALUES
  (1, 'fruit', 'I love fuji apples and juicy mangoes'),
  (2, 'tasting', 'Grape tasting notes from the orchard'),
  (3, 'fruit', 'The best juicy fuji apple in town');

CREATE INDEX posts_body_tin ON posts USING tin (body);
```

Each TIN index covers one text column (or a text-producing expression).

The default tokenizer folds case and accents and indexes emoji as terms, so `Jalapeño` and `jalapeno` match, and ”😀” is searchable. The same tokenizer applies to indexed columns and queries.

You can preview how a string is tokenized with `tin.tokenize`:

```sql
SELECT * FROM tin.tokenize('Jalapeño 😀');
SELECT * FROM tin.tokenize('Jalapeño 😀', accent_folding => 'preserve');
```

You can also create [partial indexes](reference/indexes.md#partial-and-expression-indexes), [expression indexes](reference/indexes.md#partial-and-expression-indexes), and set per-index BM25 and tokenization options with [`WITH (k1, b, tokenizer, …)`](reference/indexes.md#index-options-with). After changing analysis options on a populated index, `REINDEX` so existing rows are re-tokenized.

## Your first queries

TINQL keywords are UPPERCASE. Lowercase tokens are terms. Quote a multi-word phrase (“fuji apple”).

### Filter

```sql
SELECT id, body
FROM posts
WHERE body ==> 'apple AND "fuji apple"';
```

### Ranked results (BM25)

Order responses in a ranked list with `tin.score`

```sql
SELECT id, tin.score(ctid) AS score, body
FROM posts
WHERE body ==> 'apple OR grape'
ORDER BY score DESC
LIMIT 10;
```

To normalize scores against the query’s best match, divide by `tin.max_score(ctid)`, which is constant for the scan and identical on every row:

```sql
SELECT id,
       tin.score(ctid) AS score,
       tin.score(ctid) / tin.max_score(ctid) AS relative
FROM posts
WHERE body ==> 'apple OR grape'
ORDER BY score DESC
LIMIT 10;
```

`tin.score` and `tin.max_score` require a TIN index scan in the same query. Outside that context, they raise an error rather than returning NULL.

### Count

`count(*)` over a TIN predicate is answered from the index, not a heap scan.

```sql
SELECT count(*)
FROM posts
WHERE body ==> 'juicy';
```

### Highlight

`tin.highlight` adds markers around the text that produced the match. A match on `apple` returns `'<b>apple</b>'`.

```sql
SELECT id, tin.highlight(body)
FROM posts
WHERE body ==> 'apple'
LIMIT 20;
```

`tin.highlight` is configurable, pass in additional arguments to customize the markers and perform the search.

```sql
SELECT tin.highlight(body, '<mark>', '</mark>', 'apple')
FROM posts
WHERE id = 1;
```

### Search across columns

Each TIN index covers one text column. Index every column you want to search, then combine `==>` in SQL. `tin.score(ctid)` combines BM25 relevance across those fields for the row. To weight one column higher than another, use TINQL boost (`^N`) on that field’s query.

For example, `name ==> 'fuji^1.5'` makes a `name` match count 1.5 times an unboosted `notes` match.

```sql
CREATE TABLE fruits (
  id bigint PRIMARY KEY,
  name text NOT NULL,
  notes text NOT NULL
);

CREATE INDEX fruits_name_tin ON fruits USING tin (name);
CREATE INDEX fruits_notes_tin ON fruits USING tin (notes);

SELECT id, tin.score(ctid) AS score, name, notes
FROM fruits
WHERE name ==> 'fuji^1.5'
  AND notes ==> 'citrus'
ORDER BY score DESC
LIMIT 10;
```

## Local development and CI

[Lead](https://github.com/planetscale/lead) is a Postgres extension for exercising TIN-compatible application SQL in local development and CI. It uses the `tin` extension name so you can test your application’s search queries without connecting to PlanetScale.

Lead supports Postgres 17 and 18 and is built from source. Follow the [Lead README](https://github.com/planetscale/lead#build) for build prerequisites, local setup, and packaging instructions for your test database.

Lead is intended for small development and test datasets. It scans table rows instead of maintaining TIN’s search index, so it is not suitable for production workloads or benchmarking TIN’s performance. Use TIN on PlanetScale for production search.

## Common pitfalls

| Symptom | Fix |
| --- | --- |
| Parse error on `==>` | Fix TINQL syntax. Keywords must be UPPERCASE (`AND`, not `and` as an operator). Explicit empty syntax (`""`, `[]`) is a parse error. |
| Zero rows | Broaden the query (`OR`, fewer required terms, wildcards). Confirm you indexed the column you are searching. Check tokenization with `tin.tokenize`. Inputs that analyze to no tokens (`''`, whitespace, bare punctuation) match nothing. |
| Score / max\_score error | Call `tin.score(ctid)` / `tin.max_score(ctid)` on a query that also has `col ==> …`. They require a TIN index scan and error in other contexts (including DML `RETURNING` without a scan). On a **partitioned parent**, every planned leaf needs a usable TIN index — see [limitations](reference/limitations.md). |
| Exclusion not working | Use `AND NOT`, not `-term`. `-` is not negation. |
| `MATCHES` finds nothing | `MATCHES` is not folded. Match the dictionary form (usually lowercase, accents folded): `MATCHES apple.*`, not `MATCHES Apple.*`. |
| Accent / case mismatch | Defaults fold both. Search `jalapeno` to match `Jalapeño`. Override with `WITH (case_folding = preserve, accent_folding = preserve)` if you need exact surface forms. |
| Fuzzy error on hyphenated term | Fuzzy needs one token after tokenization. Prefer `wi-fi` as a phrase (`"wi fi"`) or drop `~N`. |
| Out-of-range `k1` / `b` / boost | `k1` and query boosts must be in `[0.0..10000.0]`; `b` must be in `[0.0..1.0]`. |
| Index keeps growing | The index relation grows to a high-water mark and does not shrink on its own. `REINDEX` shrinks it. See [limitations](reference/limitations.md). |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
