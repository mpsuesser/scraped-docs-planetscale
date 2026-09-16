---
url: https://planetscale.com/docs/postgres/search/reference/indexes
title: "Indexes"
description: ""
access_date: 2026-09-16T16:23:24.602Z
current_date: 2026-09-16T16:23:24.602Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Indexes

> Creating TIN indexes and setting their options.

**Platform availability:** Postgres only

```sql theme={null}
CREATE INDEX <index_name> ON <table_name> USING tin (<text_column>);
```

Indexes a `text` or `citext` column (or a text-producing expression) for TINQL search, including phrases, proximity, and spans. Documents and queries are analyzed with the same tokenization pipeline.

Each TIN index covers **one** text source. Multi-column `USING tin (…)` indexes are not supported. To search several columns, create one TIN index per column and combine `==>` predicates. `tin.score(ctid)` combines relevance across those columns.

```sql theme={null}
-- Create index
CREATE INDEX posts_body_tin ON posts USING tin (body);

-- Build or rebuild without blocking writes
CREATE INDEX CONCURRENTLY posts_body_tin ON posts USING tin (body);
REINDEX INDEX CONCURRENTLY posts_body_tin;
```

## Partial and expression indexes

Partial and expression TIN indexes work like other Postgres index types:

```sql theme={null}
-- Partial: only index active rows
CREATE INDEX posts_active_body_tin ON posts USING tin (body)
WHERE active;

-- Expression: index a JSON field or transformed text
CREATE INDEX docs_title_tin ON docs USING tin ((data->>'title'));
CREATE INDEX posts_lower_body_tin ON posts USING tin ((lower(body)));
```

The query must match the indexed expression exactly. `(data->>'title') ==> …` uses the expression index, and a plain `title ==> …` does not. A partial index is used only when Postgres can prove the query implies its `WHERE` predicate (for example `WHERE active AND body ==> …`).

## Index options (`WITH`)

```sql theme={null}
CREATE INDEX posts_body_tin ON posts USING tin (body)
WITH (k1 = 1.2, b = 0.75);

ALTER INDEX posts_body_tin SET (k1 = 1.5);
ALTER INDEX posts_body_tin RESET (k1);

-- Tokenization policy (same pipeline at index time and query time)
CREATE INDEX logs_msg_tin ON logs USING tin (message)
WITH (
  tokenizer = whitespace,
  case_folding = preserve,
  accent_folding = preserve,
  long_tokens = truncate,
  max_token_bytes = 64,
  graphemes = discard,
  position_gaps = collapse
);
```

### Scoring

| Option             | Type | Default | Notes                                                                                                                                                                                                                                                           |
| ------------------ | ---- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `k1`               | real | `1.2`   | BM25 term-frequency saturation. Domain `[0.0..10000.0]`, enforced at DDL.                                                                                                                                                                                       |
| `b`                | real | `0.75`  | BM25 length normalization. Domain `[0.0..1.0]`.                                                                                                                                                                                                                 |
| `score_stop_words` | text | unset   | Comma-separated terms that `tin.score` leaves out of the BM25 sum. Each entry must match the stored term exactly and is never tokenized, so write entries in analyzed form (lowercase on a case-folding index). Matching, counting, and phrases are unaffected. |

Scoring options are read at query time, so `ALTER INDEX ... SET` takes effect on the next scored query without a rebuild. `k1` and `b` can also be overridden for a single query with `tin.score(ctid, k1 => …, b => …)`. [`tin.score_inspect`](../scoring.md) shows which terms a query would score after stop words and dense-term elision are applied.

### Tokenization

| Option            | Type | Default    | Notes                                                                           |
| ----------------- | ---- | ---------- | ------------------------------------------------------------------------------- |
| `tokenizer`       | text | `unicode`  | Base token boundary policy: `unicode` or `whitespace`.                          |
| `case_folding`    | text | `fold`     | Unicode case folding: `fold` or `preserve`.                                     |
| `accent_folding`  | text | `fold`     | Accent/diacritic folding: `fold` or `preserve`.                                 |
| `long_tokens`     | text | `split`    | Tokens longer than `max_token_bytes`: `split`, `truncate`, or `discard`.        |
| `max_token_bytes` | int  | `256`      | Maximum token length in UTF-8 bytes (`[4..2692]`).                              |
| `graphemes`       | text | `emoji`    | Standalone grapheme clusters (emoji, symbols): `emoji`, `retain`, or `discard`. |
| `position_gaps`   | text | `preserve` | Position numbering when analysis removes tokens: `preserve` or `collapse`.      |

Defaults fold **case and accents**, split over-long tokens on grapheme boundaries, and emit emoji as searchable terms. Indexing and query analysis use the same pipeline, so `==>` searches agree with what the index stored.

```sql theme={null}
CREATE INDEX posts_body_tin ON posts USING tin (body);

SELECT id FROM posts WHERE body ==> 'jalapeno';  -- matches 'Jalapeño'
SELECT id FROM posts WHERE body ==> '😀';         -- emoji are terms
```

Use `tin.tokenize` to inspect the pipeline. Named arguments match the index options and use the same defaults:

```sql theme={null}
SELECT * FROM tin.tokenize('Jalapeño 😀');
-- default policy: case + accent folding, emoji as a term

SELECT * FROM tin.tokenize('Jalapeño 😀', accent_folding => 'preserve');
```

Hyphenated surface forms may still split into multiple tokens (for example `wi-fi` becomes the phrase `"wi fi"`). Fuzzy (`term~N`) requires a single token after tokenization.

Changing tokenization options on a populated index does not re-tokenize stored rows. Run `REINDEX` to apply the new policy to them.

### Segments

A TIN index is stored as segments. The segment count sets how many parallel workers a build and a query can use, and background maintenance folds new writes into segments over time.

| Option                     | Type | Default                   | Notes                                                                                                                                                  |
| -------------------------- | ---- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `initial_segment_count`    | int  | available parallelism     | Segments created at build (`[1..4096]`). Read at `CREATE INDEX` and `REINDEX`, and used as the default for `target_segment_count`.                     |
| `target_segment_count`     | int  | = `initial_segment_count` | Segment count that background maintenance converges to as writes add segments (`[1..4096]`).                                                           |
| `max_mutable_segment_size` | int  | `4194304` (4 MiB)         | Bytes of new writes that accumulate before background maintenance folds them into the index (`≥ 131072`). Folding also happens every 16,384 documents. |
| `max_merged_segment_size`  | int  | `2000`                    | Size in megabytes above which a segment is no longer merged with others (`≥ 100`).                                                                     |
| `dead_percent_threshold`   | real | `0.5`                     | Fraction of dead rows at which a segment is rewritten to drop them. Domain `[0.0..1.0]`.                                                               |

Changes to these options take effect on the next maintenance pass, except `initial_segment_count`, which is read only at build.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
