---
url: https://planetscale.com/docs/neki/monitoring/schema-recommendations
title: "Schema Recommendations"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

Schema recommendations use query-level telemetry, Postgres system tables, and your database schema to suggest DDL that can improve performance, reduce memory and storage, or prevent primary-key exhaustion.

Recommendations are available on every Neki cluster. They are also available through the [PlanetScale MCP server](../../mcp-server.md). AI coding agents can evaluate and apply them on a recurring schedule; see [Self-improving database](../../self-improving-database.md).

## Open schema recommendations

From the [PlanetScale dashboard](https://app.planetscale.com/), select a Neki database and branch, then select **Insights** and **View recommendations**.

Each open recommendation includes:

- An explanation of the change and its intended benefit, such as lower execution time, reduced storage, or prevention of ID exhaustion.
- The schema objects or queries it affects.
- DDL that applies the recommendation.

Recommendations that depend on database traffic run once per day. Recommendations that depend only on schema run when the default branch schema changes. Insights generates recommendations only for the database’s default branch.

Schema recommendations may not match your intended outcomes. Evaluate each suggestion before applying it. PlanetScale is not liable for actions you take based on these recommendations.

## Apply a recommendation

Schema recommendations include either a DDL statement or an Online DDL script. Apply it through a Neki router using native DDL or a [managed schema-change workflow](../schema-changes.md), as directed by the recommendation. You can also make the equivalent change in your application or ORM migrations.

Evaluate lock behavior, rewrite cost, and downtime before running the statement on a production branch. Application DDL is cluster-wide: Neki sends the change to every managed shard.

## Close a recommendation

Insights compares each open recommendation with the current default-branch schema and closes it when the schema shows the work was done: the suggested index exists, or the flagged index or table is gone. A recommendation also closes when other schema changes make it unnecessary.

Bloat recommendations behave differently. A rebuild leaves the schema unchanged, so Insights has no signal that it happened. A table or index bloat recommendation stays open after `VACUUM FULL` or `REINDEX INDEX CONCURRENTLY` and has to be closed manually.

Once a recommendation is closed, Insights does not suggest it again for that object. If you decide to keep the current schema, close the recommendation.

## Supported schema recommendations

Insights can suggest the following changes:

- [Adding indexes for inefficient queries](#adding-indexes-for-inefficient-queries)
- [Removing redundant indexes](#removing-redundant-indexes)
- [Preventing primary key ID exhaustion](#preventing-primary-key-id-exhaustion)
- [Dropping unused tables](#dropping-unused-tables)
- [Dropping unused indexes](#dropping-unused-indexes)
- [Rebuilding bloated tables](#rebuilding-bloated-tables)
- [Rebuilding bloated indexes](#rebuilding-bloated-indexes)

### Adding indexes for inefficient queries

Indexes often reduce rows read and query time. Insights scans query telemetry daily for expensive patterns, proposes indexes, and estimates the improvement before opening a recommendation.

A new index recommendation shows the `CREATE INDEX` DDL, the queries that benefit, and an estimated performance change.

Indexes speed up reads and increase write cost, memory use, and table size. After you apply an index, use [Query Insights](query-insights.md) to confirm the intended queries use it.

Index suggestions can use AI tools. See [How PlanetScale uses AI](../../how-we-use-ai.md). You can disable new index suggestions and other LLM-based features in organization settings.

### Removing redundant indexes

Unnecessary indexes slow writes and consume memory and storage. Insights scans schema changes for:

- Exact duplicate indexes that use the same columns in the same order.
- Left-prefix duplicate indexes that match the leading columns of a larger index.

Exact duplicates are safe to remove. Left-prefix duplicates are usually safe, but removing one can regress queries that relied on the shorter index.

### Preventing primary key ID exhaustion

Sequence-backed primary keys can reach the maximum value of their integer type. Further inserts then fail.

Insights checks sequence-owned columns daily. When a column has reached 60% or more of its type maximum, it recommends a larger type, typically `BIGINT`. It can also suggest widening foreign-key columns that join to that primary key.

Widening a column rewrites every row, and a routed `ALTER TABLE` would hold an exclusive lock on the table cluster-wide until the rewrite finished. The recommendation therefore shows an [Online DDL](../schema-changes.md) script instead, which builds the new table beside the original and keeps the live table available for reads and writes. The script has four steps: create the migration, poll until every shard is ready, cut over, then clean up the shadow tables and replication slots.

Prefer `BIGINT` primary keys unless you know the table will stay small.

### Dropping unused tables

Insights flags tables that are more than four weeks old and have not been queried in the last four weeks. Dropping an unused table can reduce storage and shorten backups.

Create a [manual backup](../backups.md) before dropping a table if you are unsure it can be discarded. Confirm that the application no longer uses the table. If the table should be kept, close the recommendation.

Once opened, an unused-table recommendation stays open even if the table is queried later. Check Query Insights before dropping it.

### Dropping unused indexes

Insights flags indexes that are more than four weeks old and have not been used in the last four weeks. Removing them can lower write cost and save memory and storage.

Dropping an unused index can still affect future queries. Confirm that no workload depends on it. Filter Query Insights with `index:index_name` to check recent use. Once opened, the recommendation stays open even if the index is used later.

### Rebuilding bloated tables

Postgres MVCC leaves dead tuples after updates and deletes. High table bloat wastes disk, slows queries, and lengthens backups and DDL.

Insights estimates bloat daily from system tables. A recommendation opens when estimated wasted space is over 25% and 100 MB for a table.

The recommendation shows `VACUUM FULL`. That statement takes an exclusive lock on the table and rewrites it on every shard. Do not run it on a table your application is actively using. It is not an online operation, and a large table can take hours.

`VACUUM FULL` writes a new copy of the table and its indexes. Each shard needs free disk space for a second copy of its portion of the table, plus a safety buffer.

Plain `VACUUM` is not a substitute. It makes dead space reusable but does not return it to the operating system, so the recommendation stays open.

Bloat can also come from long-running transactions or infrequent vacuuming. Address those causes if a rebuild does not hold. Once opened, a bloat recommendation stays open even if bloat later drops below the threshold. Once closed, it is not opened again for that table.

### Rebuilding bloated indexes

Index bloat has the same MVCC cause as table bloat. Insights opens a recommendation when estimated wasted space is over 30% and 100 MB for an index. The recommendation typically shows `REINDEX INDEX CONCURRENTLY ...`.

`REINDEX INDEX CONCURRENTLY` consumes database resources. Confirm that the branch has enough capacity before rebuilding large indexes. The same open-and-close rules as table bloat apply.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
