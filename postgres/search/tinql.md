---
url: https://planetscale.com/docs/postgres/search/tinql
title: "Tinql"
description: ""
access_date: 2026-09-16T16:23:24.602Z
current_date: 2026-09-16T16:23:24.602Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# TINQL

> A fully-featured query language unique to TIN.

**Platform availability:** Postgres only

TIN adds [the `==>` operator](reference/operator.md). The query on the right-hand side of it is **TINQL**.

```sql theme={null}
SELECT id, body
FROM posts
WHERE body ==> 'apple AND "fuji apple"';
```

**Rules**

* Keywords must be **UPPER CASE** (`AND`, `OR`, `THEN`). Lowercase is a search term (`and` matches the word “and”).
* Matching **folds case and accents** under the default tokenizer (`Jalapeño` and `jalapeno` are the same term). Override with `case_folding` / `accent_folding` on the index.
* Inputs that analyze to **no tokens** (`''`, whitespace, bare punctuation) match nothing. Explicit empty syntax (`""`, `[]`) is a parse error.
* Query boost factors (`^N`) must be in `[0.0..10000.0]`. The system rejects out-of-range values at parse time.
* Token positions are **0-based**. Prefer `IN FIRST N WORDS` / `IN LAST N WORDS` over raw `IN WORDS` ranges when you mean “the first/last N words.”
* Juxtaposition defaults to AND: `apple grape` acts as `apple AND grape`.
* Exclude terms with `AND NOT`: `apple AND NOT peel`.
* Quote reserved words used as terms:  `"AND"`, `"THEN"`.
* A hyphen usually splits a term: `wi-fi` is searched as `"wi fi"`.
* Fuzzy match (`apple~2`) works on one word. `wi-fi~2` errors because `wi-fi` is two words.

## Terms

| Form                | Meaning                                                   | Example                                                    |
| ------------------- | --------------------------------------------------------- | ---------------------------------------------------------- |
| bare term           | Single dictionary term (case + accent folded by default)  | `apple` (same as `Apple`), `jalapeno` (same as `Jalapeño`) |
| `*`                 | Match-all documents (standalone only)                     | `*`                                                        |
| `*` / `?` wildcards | `*` = 0+ chars, `?` = 1 char                              | `appl*`, `p?ach`, `*house`                                 |
| `term~N`            | Fuzzy: edit distance N, stable prefix 1                   | `apple~2`                                                  |
| `term~P:N`          | Fuzzy: stable prefix P, distance N                        | `apple~0:2`                                                |
| `A TO B`            | Inclusive term-dictionary range                           | `aardvark TO cat`                                          |
| `* TO B` / `A TO *` | Open range bound                                          | `* TO cat`                                                 |
| `MATCHES regex`     | Full-term regex (to unescaped whitespace); **not** folded | `MATCHES peach.*`                                          |
| `CONTAINS term`     | Same as bare term (optional)                              | `CONTAINS apple`                                           |
| `expr^N`            | Boost (`N` ∈ `[0.0..10000.0]`)                            | `apple^2`, `"x"^1.5`                                       |

Allowed in terms: anything except whitespace and `( ) [ ] " ~ ^`. Escape literal `*` / `?` with `\`. Space in a regex: `MATCHES foo\ bar`.

Because `MATCHES` is not folded, write patterns against the normalized dictionary form (usually lowercase, accents folded): `MATCHES apple.*`, not `MATCHES Apple.*`.

## Phrases

| Form          | Meaning                                    | Example                  |
| ------------- | ------------------------------------------ | ------------------------ |
| `"a b c"`     | Adjacent ordered words                     | `"fuji apple"`           |
| `"a _ c"`     | `_` = one any-word gap                     | `"big _ wolf"`           |
| `"a [b c] d"` | Per-position alternatives                  | `"big [bad large] wolf"` |
| `"a b"~N`     | Up to N extra word gaps (phrase tolerance) | `"fuji apple"~2`         |

Phrase escapes: `\"` `\\` `\_` `\[` `\]`. Empty `""` is invalid (parse error). Inputs that analyze to no tokens match nothing.

## Boolean operators

| Operator      | Meaning                  | Example              |
| ------------- | ------------------------ | -------------------- |
| `A AND B`     | Both match               | `juicy AND apple`    |
| `A OR B`      | Either matches           | `apple OR grape`     |
| `A AND NOT B` | A matches and B does not | `apple AND NOT peel` |
| `A B`         | Implicit AND             | `apple grape`        |

`NOT` never stands alone. It only appears in `AND NOT`, `NOT ENCLOSES`, `NOT ENCLOSED BY`, and `NOT OVERLAPPING`.

## Alternatives

| Form                 | Meaning                                      | Example                     |
| -------------------- | -------------------------------------------- | --------------------------- |
| `[a b c]`            | OR of whitespace-separated alts (not commas) | `[mango plum pear]`         |
| `AT LEAST N OF […]`  | At least N alts match                        | `AT LEAST 2 OF [a b c d]`   |
| `AT LEAST N% OF […]` | At least N% of alts match                    | `AT LEAST 50% OF [a b c d]` |
| `ALL OF […]`         | Every alt matches                            | `ALL OF [a b c]`            |

Alternatives may hold expressions: `["big bad" house NEAR/2 brick]`.

## Proximity

`/N` is required on `THEN` and `NEAR`. N is the maximum number of extra words allowed between the operands, and `0` means adjacent.

| Operator           | Meaning              | Example                          |
| ------------------ | -------------------- | -------------------------------- |
| `A THEN/N B`       | Ordered proximity    | `fuji THEN/0 apple`              |
| `A NEAR/N B`       | Either order         | `peach NEAR/5 blossom`           |
| `(A … B) WITHIN N` | Match span width ≤ N | `(citrus NEAR/5 melon) WITHIN 6` |

## Span relations

Every TINQL expression produces **spans** (position ranges). Relation operators compare two span sets.

| Operator              | Keeps                                     | Example                                                |
| --------------------- | ----------------------------------------- | ------------------------------------------------------ |
| `A ENCLOSES B`        | Spans of A that contain a span of B       | `(security NEAR/10 threat) ENCLOSES critical`          |
| `A NOT ENCLOSES B`    | Spans of A that contain no span of B      | `* NOT ENCLOSES spam`                                  |
| `A ENCLOSED BY B`     | Spans of A that fall inside a span of B   | `critical ENCLOSED BY (security NEAR/10 threat)`       |
| `A NOT ENCLOSED BY B` | Spans of A not inside any span of B       | `price NOT ENCLOSED BY (disclaimer NEAR/20 terms)`     |
| `A OVERLAPPING B`     | Spans of A that share a position with B   | `(apple NEAR/5 fuji) OVERLAPPING (kiwi NEAR/5 citrus)` |
| `A NOT OVERLAPPING B` | Spans of A that share no positions with B | `title NOT OVERLAPPING disclaimer`                     |
| `A BEFORE B`          | Spans of A that start before B            | `abstract BEFORE conclusion`                           |
| `A AFTER B`           | Spans of A that start after B             | `price AFTER discount`                                 |

`ENCLOSES` and `ENCLOSED BY` test the same relationship but return different spans: `ENCLOSES` returns the outer span and `ENCLOSED BY` returns the inner one.

## Positional filters

Token positions are **0-based** (the first token is position `0`). Use `IN FIRST` / `IN LAST` when you care about word counts, and `IN WORDS` when you need an explicit position window.

| Form                 | Meaning                                             | Example                      |
| -------------------- | --------------------------------------------------- | ---------------------------- |
| `A IN FIRST N WORDS` | Match in the first N tokens (positions `0` … `N-1`) | `apple IN FIRST 100 WORDS`   |
| `A IN FIRST N%`      | Match in first N% of the document                   | `apple IN FIRST 25%`         |
| `A IN LAST N WORDS`  | Match in the last N tokens                          | `apple IN LAST 50 WORDS`     |
| `A IN LAST N%`       | Match in last N% of the document                    | `apple IN LAST 25%`          |
| `A IN MIDDLE N%`     | Match in middle N% of the document                  | `apple IN MIDDLE 50%`        |
| `A IN WORDS X TO Y`  | Match in inclusive 0-based position range `[X, Y]`  | `apple IN WORDS 500 TO 1000` |

## Precedence (loose → tight)

| Level | Operators                                           |
| ----- | --------------------------------------------------- |
| 1     | `OR`                                                |
| 2     | `AND` (explicit and implicit)                       |
| 3     | `AND NOT`                                           |
| 4     | Positional filters (`IN …`)                         |
| 5     | Relation operators (`ENCLOSES`, `BEFORE`, …)        |
| 6     | Proximity (`THEN/N`, `NEAR/N`)                      |
| 7     | `WITHIN`                                            |
| 8     | Boost (`^N`)                                        |
| 9     | Primary (`apple`, `"phrase"`, `[a b]`, `MATCHES …`) |

Operators at the same level are left-associative. Use parentheses to override precedence.

`A OR B THEN/5 C AND D` → `A OR ((B THEN/5 C) AND D)`.

Postfix modifiers compose on the primary: `apple~2^3`, `"big bad"~2^1.5`.

## Keyword index

All keywords are case-sensitive **UPPER CASE**.

| Category         | Keywords                                                       |
| ---------------- | -------------------------------------------------------------- |
| Boolean          | `AND`, `OR`, `NOT` (only in compounds)                         |
| Proximity        | `THEN`, `NEAR`, `WITHIN`                                       |
| Relations        | `ENCLOSES`, `ENCLOSED`, `BY`, `OVERLAPPING`, `BEFORE`, `AFTER` |
| Range / position | `TO`, `IN`, `FIRST`, `LAST`, `MIDDLE`, `WORDS`                 |
| Terms            | `CONTAINS`, `MATCHES`                                          |
| Alternatives     | `AT`, `LEAST`, `OF`, `ALL`                                     |

`IN` is special only before `FIRST` / `LAST` / `MIDDLE` / `WORDS`. `AT` only before `LEAST`. `ALL` only before `OF`. `ENCLOSED` / `BY` only together.

## Special characters

| Character | Meaning                                                  |
| --------- | -------------------------------------------------------- |
| `"…"`     | Phrase                                                   |
| `(…)`     | Grouping                                                 |
| `[…]`     | Alternatives                                             |
| `~N`      | Fuzzy (on term) or phrase word-gap tolerance (after `"`) |
| `~P:N`    | Fuzzy with explicit prefix length                        |
| `^N`      | Boost                                                    |
| `*`       | Wildcard, open range bound, or standalone match-all      |
| `?`       | Single-character wildcard                                |
| `_`       | One-word gap inside a phrase                             |
| `\`       | Escape                                                   |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
