---
url: https://planetscale.com/docs/postgres/search/reference/operator
title: "Operator"
description: ""
access_date: 2026-09-16T16:23:24.602Z
current_date: 2026-09-16T16:23:24.602Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# The ==> operator

> Search a TIN-indexed column with a TINQL query string.

**Platform availability:** Postgres only

TIN introduces the `==>` operator which is followed by a TINQL query.

```sql theme={null}
SELECT id, body
FROM posts
WHERE body ==> 'apple AND "fuji apple"';
```

| Side  | Type   | Notes                                                             |
| ----- | ------ | ----------------------------------------------------------------- |
| Left  | `text` | Indexed text column or matching expression.                       |
| Right | `text` | TINQL query string. May be a prepared-statement parameter (`$1`). |

Returns `true` when the document matches the TINQL expression. An input that analyzes to no tokens (`''`, whitespace, bare punctuation) matches nothing. Explicit empty syntax (`""`, `[]`) is a parse error. Combine multiple `==>` predicates and ordinary SQL filters with `AND` / `OR`. When several indexed columns appear in the same query, `tin.score(ctid)` combines BM25 relevance across those fields.

The right-hand side may also be an array via `==> ANY (…)`, which matches if any element matches:

```sql theme={null}
SELECT *
FROM posts
WHERE body ==> 'juicy AND apple';

-- Zero-token input matches nothing; explicit empty syntax is a parse error
SELECT count(*) FROM posts WHERE body ==> '';
SELECT count(*) FROM posts WHERE body ==> '""';  -- ERROR

-- Match any of several TINQL strings
SELECT id FROM posts
WHERE body ==> ANY (ARRAY['apple', 'grape']);

-- Cross-column: one TIN index per column, combine predicates
SELECT id, tin.score(ctid) AS score, name, notes
FROM fruits
WHERE name ==> 'fuji'
  AND notes ==> 'citrus'
ORDER BY score DESC
LIMIT 10;
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
