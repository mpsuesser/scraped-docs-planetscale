---
url: https://planetscale.com/docs/postgres/search/reference/functions
title: "Functions"
description: ""
access_date: 2026-09-16T16:23:24.602Z
current_date: 2026-09-16T16:23:24.602Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Functions

> SQL functions in the `tin` schema.

**Platform availability:** Postgres only

## `tin.score(ctid [, dense_ratio [, k1 [, b [, term_add [, term_replace]]]]])`

`tin.score(tid, real, real, real, text[], text[]) → real`

Returns the BM25 relevance of the current row, leaving out dense terms. See [Scoring](../scoring.md).

## `tin.full_score(ctid [, k1, b])`

`tin.full_score(tid) → real`\
`tin.full_score(tid, real, real) → real`

Returns the BM25 relevance of the current row with every query term kept in the sum. See [Scoring](../scoring.md).

## `tin.max_score(ctid)`

`tin.max_score(tid) → real`

Returns the highest score among the query's matches. The value is the same on every row. See [Scoring](../scoring.md).

## `tin.score_inspect(index, query [, dense_ratio [, term_add [, term_replace]]])`

`tin.score_inspect(regclass, text, real, text[], text[]) → setof (term text, weight real)`

Returns the terms a `tin.score` call with the same arguments would score, with their boost weights. See [Scoring](../scoring.md).

## `tin.highlight(text [, begin_tag [, end_tag [, query]]])`

`tin.highlight(text, text, text, text) → text`

Returns the document text with matched spans wrapped in tags. See [Highlighting](../highlighting.md).

## `tin.highlight_ansi(text [, wrap_to [, query]])`

`tin.highlight_ansi(text, integer, text) → text`

Returns the document text with matched spans in ANSI colors. See [Highlighting](../highlighting.md).

## `tin.tokenize(text [, …])`

`tin.tokenize(text) → setof text`

Returns the tokens the analysis pipeline produces for a string. Optional named arguments match the [index tokenization options](indexes.md#index-options-with) and use the same defaults (`tokenizer`, `case_folding`, `accent_folding`, `long_tokens`, `max_token_bytes`, `graphemes`, `position_gaps`). Useful when debugging why a query does or does not match.

```sql theme={null}
SELECT * FROM tin.tokenize('Jalapeño 😀');
SELECT * FROM tin.tokenize('Jalapeño 😀', accent_folding => 'preserve');
```

## `tin.maybe_quote(text)`

`tin.maybe_quote(text) → text`

Quotes a term for safe use inside a TINQL string when it would otherwise be parsed as a keyword or special form.

```sql theme={null}
SELECT tin.maybe_quote('AND');
-- "AND"
```

## `tin.fsck(index [, heapcheck])`

`tin.fsck(regclass, boolean) → setof record`

Runs a read-only structural check of a TIN index. An empty result means the index passed. It never writes or repairs. Rebuild with `REINDEX`. Requires ownership of the index.

| Argument    | Type       | Default | Notes                                                       |
| ----------- | ---------- | ------- | ----------------------------------------------------------- |
| `index`     | `regclass` | —       | TIN index to validate.                                      |
| `heapcheck` | `boolean`  | `false` | When `true`, also cross-checks index TIDs against the heap. |

```sql theme={null}
SELECT * FROM tin.fsck('posts_body_tin');
SELECT * FROM tin.fsck('posts_body_tin', heapcheck => true);
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
