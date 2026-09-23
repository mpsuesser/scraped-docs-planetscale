---
url: https://planetscale.com/docs/postgres/search/highlighting
title: "Highlighting"
description: ""
access_date: 2026-09-23T03:58:51.015Z
current_date: 2026-09-23T03:58:51.015Z
---

`tin.highlight` adds configurable markers around the text that produced the match. A match on `pineapple` returns `'<b>pineapple</b>'`. `tin.highlight_ansi` does the same with ANSI colors for a terminal.

## tin.highlight(text \[, begin\_tag \[, end\_tag \[, query\]\]\])

`tin.highlight(text, begin_tag, end_tag, query) → text`

| Argument | Type | Default | Notes |
| --- | --- | --- | --- |
| `text` | `text` | — | Document text to highlight (usually the indexed column). |
| `begin_tag` | `text` | `'<b>'` | Opening tag wrapped around each match. May contain the placeholders described under [Tag placeholders](#tag-placeholders). |
| `end_tag` | `text` | `'</b>'` | Closing tag wrapped around each match. |
| `query` | `text` | `NULL` | TINQL used to find match spans. When omitted, the `==>` predicate on the same column in the statement’s `WHERE` clause is used. |

Returns document text with matched spans wrapped in tags. Highlighting marks exactly the text that produced the match. For `a BEFORE b`, only the `b` occurrences that satisfy the relation are wrapped.

When `query` is omitted, the predicate is found anywhere in the same statement, including subqueries, CTEs, and DML (`RETURNING`, `MERGE` actions, `ON CONFLICT`).

```sql
-- Query taken from the ==> predicate on body
SELECT id, tin.highlight(body)
FROM posts
WHERE body ==> 'apple'
LIMIT 20;

-- Witnessing spans only
SELECT tin.highlight('b a b', query => 'a BEFORE b');
--  b <b>a</b> <b>b</b>      (the leading b precedes no a — unmarked)

-- Automatic query in DML RETURNING
UPDATE posts SET reviewed = true
WHERE body ==> 'urgent'
RETURNING id, tin.highlight(body);

-- Explicit tags + query
SELECT tin.highlight(body, '<mark>', '</mark>', 'apple')
FROM posts
WHERE id = 1;
```

## Tag placeholders

`begin_tag` can carry two placeholders that TIN fills in for each highlighted span.

| Placeholder | Replaced with |
| --- | --- |
| `$QUERY_PART` | The part of the query that matched the span, HTML-escaped. For a wildcard or regular expression this is the pattern that matched, such as `MATCHES email.*`. |
| `$QUERY_LABEL` | The same information as a token that is safe in a CSS class: lowercased, with punctuation collapsed to `-`. |

```sql
SELECT tin.highlight('send email now', '<b title="$QUERY_PART">', '</b>', 'email*');
--  send <b title="MATCHES email.*">email</b> now

-- One CSS class per query part
SELECT id, tin.highlight(body, '<mark class="hl-$QUERY_LABEL">', '</mark>')
FROM posts
WHERE body ==> 'apple OR "fuji apple"';
```

When two matches overlap or touch, they merge into one highlight whose placeholders list every query part involved, so tags never nest or interleave.

## tin.highlight\_ansi(text \[, wrap\_to \[, query\]\])

`tin.highlight_ansi(text, wrap_to, query) → text`

| Argument | Type | Default | Notes |
| --- | --- | --- | --- |
| `text` | `text` | — | Document text to highlight. |
| `wrap_to` | `integer` | `NULL` | When set to a positive width, rewraps the text before highlighting. |
| `query` | `text` | `NULL` | TINQL used to find match spans. When omitted, the `==>` predicate on the same column in the statement’s `WHERE` clause is used. |

Returns document text with ANSI color highlighting for matched spans.

```sql
SELECT tin.highlight_ansi(body)
FROM posts
WHERE body ==> 'apple'
LIMIT 5;
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
