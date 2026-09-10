---
url: https://planetscale.com/docs/neki/monitoring/query-insights
title: "Query Insights"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Query Insights

> Analyze query latency, resource use, and cross-shard execution in Neki.

**Platform availability:** [Vitess](../../vitess/monitoring/query-insights.md) and [Postgres](../../postgres/monitoring/query-insights.md)

Query Insights groups executions of the same normalized SQL into query patterns.
Use it to find queries that run frequently, consume a large share of database
time, read unnecessary rows, fan out across shards, or use more parallel workers
than expected.

Insights is available on every Neki cluster. From the same tab you can also
open [Anomalies](anomalies.md) and
[schema recommendations](schema-recommendations.md).

## Open Query Insights

From the [PlanetScale dashboard](https://app.planetscale.com), select a Neki
database and branch, then select **Insights**.

The page opens to the last 24 hours. Use the branch selector to choose the
branch you want to analyze.

## Explore query activity

The graph provides the following views:

* **Query latency** shows p50 and p95 latency by default. You can also display
  p99, p99.9, and maximum latency.
* **Queries** shows the query rate over the selected period.
* **Rows read** and **Rows written** show row activity over the selected period.
* The final tab graphs one additional query metric that you select.

Select a day from the previous seven days or use the **Last** menu to view the
last 15 minutes, 1 hour, 3 hours, 6 hours, 12 hours, or 24 hours. You can also
drag across a graph to examine a smaller time range, change the automatic
refresh interval, or save the graph as an image.

## Query patterns

Insights replaces literal values with numbered placeholders so executions that
differ only by their values can be grouped together. For example, these
executions belong to the same pattern:

```sql theme={null}
SELECT * FROM orders WHERE customer_id = 17;
SELECT * FROM orders WHERE customer_id = 42;
```

The normalized pattern uses a placeholder:

```sql theme={null}
SELECT * FROM orders WHERE customer_id = $1;
```

Normalized SQL is not the only thing that separates patterns. A router groups
executions by the database the session is connected to, what the statement's
names resolved to, and whether the execution ran on a primary or a replica. The
same SQL text can therefore appear as more than one row: once for each
`search_path` resolution that produced a different set of relations, and again
for the replica traffic that ran it.

### Pattern cardinality

Each router tracks up to 2,000 patterns per aggregation interval. Additional
patterns are combined into an overflow entry, so not every distinct pattern is
listed. A branch that runs many distinct statement shapes, such as generated
SQL with inlined literals that normalization cannot collapse, reaches this
limit most often.

The table below the graph summarizes each pattern. Use the **Overview**,
**Data**, **Resources**, and **Performance** presets to switch between related
columns, or select **Custom** to choose columns individually. Numeric columns
can display sparklines for the selected time range.

The default **Overview** preset includes the query, percentage of runtime,
execution count, total time, p50 and p99 latency, rows read, and the ratio of
rows read to rows returned.

### Table and schema names in Neki

A Postgres query can use an unqualified table name, such as `orders`, while
`search_path` determines the schema that Postgres actually uses. Four columns
describe the result of that resolution:

| Column              | Example              | Meaning                                                          |
| ------------------- | -------------------- | ---------------------------------------------------------------- |
| **Table**           | `orders`             | The relation name without its database or schema.                |
| **Qualified table** | `appdb.sales.orders` | The complete resolved relation name, as `database.schema.table`. |
| **Table schema**    | `appdb.sales`        | The database and schema of each resolved relation.               |
| **Schema**          | `appdb.a91f4c02`     | The connection grouping the pattern was recorded under.          |

For example, `SELECT * FROM orders` can resolve to `appdb.sales.orders`. Use
**Qualified table** when you need to know which database object Neki queried. If
a pattern accesses multiple tables, **Qualified table** and **Table schema**
list a value for each of them.

**Schema** is not a PostgreSQL schema name on Neki. Its value combines the
connected database with an identifier for what the statement's names resolved
to, so two rows with the same SQL and different **Schema** values resolved to
different relations. Read the actual schema from **Table schema** or
**Qualified table**.

### Available query statistics

The Neki query table can show the following groups of statistics. A column is
shown only when its data is collected for the selected branch.

| Group           | Statistics                                                                                                                                     |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| Query identity  | Query, Schema, Table schema, Qualified table, Table                                                                                            |
| Time            | % of runtime, % of CPU time, % of IO time, Count, Total time, CPU time, I/O time, Last run                                                     |
| Latency         | p50 latency, p99 latency, Max latency                                                                                                          |
| Rows            | Rows read, Rows read per query, Rows returned, Rows returned per query, Rows affected, Rows affected per query, Rows read / returned           |
| Routing         | Shard calls per query, Max shard calls per query, Parallel workers per query                                                                   |
| Postgres blocks | Block cache hit ratio, Blocks hit, Blocks read, Blocks dirtied, Blocks written                                                                 |
| Network         | Bytes returned, Bytes returned per query, Max bytes returned per query, Bytes received, Bytes received per query, Max bytes received per query |

**Shard calls per query** is the average number of shard dispatches per
execution of the pattern, and **Max shard calls per query** is the highest
number recorded for a single execution. A query scattered to four shards
records four shard calls. Several statements produced by a rewrite within one
dispatch count as one shard call, and repeated dispatches to the same shard
count separately, so these metrics are not a count of distinct shards.

Buffered or replayed work and failed scatter executions can make the reported
count lower than the work performed.

A value greater than one indicates either multi-shard execution or repeated
work on a shard. Review the query plan and the database's
[data topology](../data-topology.md) when investigating an unexpected value.

**Parallel workers per query** is the average number of Postgres backend
processes that executed the statement, counted across every shard it reached.
The count includes the leader process, so a statement that ran without
parallelism on a single shard reports one, and a statement that reached several
shards adds up each shard's processes. Parallel maintenance workers, such as
those used by an index build, count the same way. This describes process use
inside Postgres and is separate from the number of shard calls.

## Filter query patterns

The filter field accepts free text and named terms. Select **SYNTAX** beside the
field to see the operators the dashboard supports.

```text theme={null}
statement_type:select
table:orders
table_schema:appdb.sales
multishard:true
indexed:false
query_count:>5000
p50:<25
p99:>250
max_latency:>1000
index:orders_customer_id_idx
```

### Filter on tables and schemas

Four named terms filter on the relations a pattern resolved to. All of them
match case-insensitively.

| Term                       | Matches                                                                                                                                                                                                             |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `table:orders`             | Any resolved relation named `orders`, in any database and schema.                                                                                                                                                   |
| `table:sales.orders`       | A resolved relation whose qualified name ends in `sales.orders`. A fully qualified value, such as `table:appdb.sales.orders`, narrows the match further.                                                            |
| `table_schema:appdb.sales` | Patterns that resolved a relation in the `sales` schema of `appdb`. Neki records this value as `database.schema`, so both halves are required.                                                                      |
| `schema:appdb.a91f4c02`    | The connection grouping described in [Table and schema names in Neki](#table-and-schema-names-in-neki). This term requires the whole value, so copy it from the **Schema** column rather than typing a schema name. |

`qualified_table:orders` finds patterns that name `orders` with an explicit
qualifier in their SQL. It accepts a bare relation name only, so use `table:`
when you want to filter by a database or schema prefix.

### Free text

A term with no name matches the normalized SQL with a case-insensitive
substring comparison. Free text does not search the resolved table metadata, so
`appdb.sales.orders` finds only patterns whose SQL text contains that string,
not every pattern that resolved to that relation. Use `table:` or
`table_schema:` for the resolved relation instead.

A named term whose value the term type rejects becomes free text as well. Because
`qualified_table:` accepts only a bare relation name,
`qualified_table:appdb.sales.orders` searches SQL text rather than table
metadata.

### Other filter behavior

Latency filter values are in milliseconds. Wrap text in double quotes for an
exact string match, and prefix a term with `!` to exclude it. Tag filters are
also available when query dimensions are enabled for the branch.

## Query-pattern details

Select a query pattern to open its detail page. The page shows the normalized
SQL and graphs the selected pattern separately from the rest of the branch. It
also provides summary statistics for count, total time, rows read relative to
rows returned, p50 latency, p99 latency, and errors.

Depending on the statement and the data collected for the branch, the detail
page can also show:

* Index usage over the selected time range.
* Notable executions that were slow, read a large number of rows, or returned
  an error.
* Query tags and their values.
* An optional AI-generated summary of the normalized query. See
  [How PlanetScale uses AI](../../how-we-use-ai.md).

## Anomalies and schema recommendations

Insights also surfaces two related views for every Neki cluster:

* [Anomalies](anomalies.md) flags periods when a high share of
  queries run slower than their established baseline.
* [Schema recommendations](schema-recommendations.md) suggest DDL
  that can improve performance, reduce storage, or prevent ID exhaustion.

From **Insights**, open **Anomalies** or **View recommendations**.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
