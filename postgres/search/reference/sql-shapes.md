---
url: https://planetscale.com/docs/postgres/search/reference/sql-shapes
title: "Sql Shapes"
description: ""
access_date: 2026-09-16T16:23:24.602Z
current_date: 2026-09-16T16:23:24.602Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Recommended SQL shapes

> Query shapes that use the index well.

**Platform availability:** Postgres only

The examples on this page use two tables. `posts` has TIN indexes on `title` and `body` and btree indexes on `author_id` and `created_at`. `authors` has a TIN index on `bio`.

```sql theme={null}
CREATE TABLE authors (
  id     bigint PRIMARY KEY,
  name   text NOT NULL,
  bio    text NOT NULL,
  topics text NOT NULL
);

CREATE INDEX authors_bio_tin ON authors USING tin (bio);

CREATE TABLE posts (
  id         bigint PRIMARY KEY,
  author_id  bigint NOT NULL REFERENCES authors (id),
  title      text NOT NULL,
  body       text NOT NULL,
  ups        integer NOT NULL DEFAULT 0,
  created_at timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX posts_title_tin ON posts USING tin (title);
CREATE INDEX posts_body_tin ON posts USING tin (body);
CREATE INDEX posts_author_id ON posts (author_id);
CREATE INDEX posts_created_at ON posts (created_at);
```

## Ranked results

```sql theme={null}
-- $1 is the TINQL query, $2 the page size
SELECT id, title, tin.score(ctid) AS score
FROM posts
WHERE body ==> $1
ORDER BY score DESC
LIMIT $2;
```

`ORDER BY tin.score(ctid) DESC LIMIT k` is the shape TIN is built around. It returns the k best rows in score order and stops as soon as it has them. Passing the query as a parameter keeps user input out of the SQL text.

```sql theme={null}
SELECT id, title,
       tin.score(ctid) AS score,
       tin.score(ctid) / tin.max_score(ctid) AS relative
FROM posts
WHERE body ==> $1
ORDER BY score DESC
LIMIT $2;
```

`tin.max_score(ctid)` is the best score among the matches and has the same value on every row, so dividing by it gives a relative score between 0 and 1. That is the number to show in a relevance bar, or to compare against a threshold when you want to hide weak matches.

## Filters and counts

```sql theme={null}
SELECT id, title
FROM posts
WHERE body ==> '"fuji apple"';

SELECT count(*)
FROM posts
WHERE body ==> 'error OR fail';
```

Without `tin.score`, `==>` is an ordinary boolean filter. A `count(*)` over a `==>` predicate is answered from the index.

## Several TIN-indexed columns

```sql theme={null}
SELECT id, title, tin.score(ctid) AS score
FROM posts
WHERE title ==> 'espresso^2'
  AND body ==> 'grinder OR "burr grinder"'
ORDER BY score DESC
LIMIT 10;
```

Each TIN index covers one column, so a query over several columns uses one `==>` per column. `tin.score(ctid)` adds up the relevance from every column that matched. The boost on the title query makes a title hit count twice as much as a body hit, which is the usual way to say that one field matters more than another.

```sql theme={null}
SELECT id, title, tin.score(ctid) AS score
FROM posts
WHERE title ==> 'espresso' OR body ==> 'espresso'
ORDER BY score DESC
LIMIT 10;
```

With `OR`, a row qualifies when either column matches, and its score sums whatever matched. This is the shape for a single search box that should look in several fields.

## Filtering on an unindexed column

```sql theme={null}
SELECT id, title, tin.score(ctid) AS score
FROM posts
WHERE body ==> 'espresso'
  AND ups > 100
ORDER BY score DESC
LIMIT 10;
```

`ups` has no index. TIN finds the rows that match the text and the `ups` condition is checked on each of them, so it costs one comparison per candidate and needs no index of its own. When the filter rejects most of the text matches, the search has to look further down the ranking to fill the limit, so this shape suits filters that keep a reasonable share of the matches.

## Combining with btree indexes

```sql theme={null}
-- Point lookup
SELECT id, title
FROM posts
WHERE body ==> 'espresso'
  AND author_id = 42;

-- Range
SELECT id, title, tin.score(ctid) AS score
FROM posts
WHERE body ==> 'espresso'
  AND created_at >= now() - interval '7 days'
ORDER BY score DESC
LIMIT 10;
```

When the other condition has a btree index, TIN can use both indexes. A selective point or range condition narrows the text search rather than filtering its results afterwards, which matters when the text query matches many rows and the other condition matches few. A recency window over a large corpus is the common case.

```sql theme={null}
SELECT id, title
FROM posts
WHERE body ==> '"espresso machine"'
   OR id = 21462;
```

With `OR`, each index contributes its own rows and the result is the union without duplicates. An `OR` costs about what its more expensive side costs, so a cheap phrase plus a primary-key lookup stays cheap.

## Joins with a score on each side

```sql theme={null}
SELECT p.id, p.title,
       tin.score(p.ctid) AS post_score,
       tin.score(a.ctid) AS author_score
FROM posts p
JOIN authors a ON a.id = p.author_id
WHERE p.body ==> 'espresso'
  AND a.bio ==> 'barista OR roaster'
ORDER BY post_score + author_score DESC
LIMIT 10;
```

`tin.score` takes the `ctid` of the relation it scores, so a join can carry one score per side. Each side needs its own `==>` predicate. A side with no text predicate has nothing to score, and the query is refused. The two scores are independent BM25 values from two different indexes, so combine them deliberately: add them, weight one, or order by one and display the other.

```sql theme={null}
SELECT a.name, p.id, p.title, p.score
FROM authors a
CROSS JOIN LATERAL (
  SELECT id, title, tin.score(ctid) AS score
  FROM posts
  WHERE body ==> a.topics
  ORDER BY score DESC
  LIMIT 3
) p
ORDER BY a.name, p.score DESC;
```

The right-hand side of `==>` can be a column from the outer query, so here the search is bound once per author using that author's `topics` as the TINQL query. A `LATERAL` subquery with `ORDER BY tin.score(ctid) DESC LIMIT k` returns the top k for each outer row. This is the shape behind "recommended for you" lists and any page that shows the best matches per user, per category, or per saved search.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
