---
url: https://planetscale.com/docs/postgres/monitoring/query-insights
title: "Query Insights"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Query Insights

> PlanetScale Insights gives you a detailed look into **all active queries** running against your database.

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

export const YouTubeEmbed = ({id, title}) => {
  return <Frame>
      <iframe src={`https://www.youtube-nocookie.com/embed/${id}?rel=0`} title={title} className="aspect-video w-full" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" />
    </Frame>;
};

<PlatformAvailability current="postgres" vitess="/vitess/monitoring/query-insights" />

## Overview

This in-dashboard tool allows you to identify queries that are running too often, too long, returning too much data, producing errors, and more. You can scroll through the performance graph to detect the time of impacted performance, quickly identifying any recent issues.

You can also see a [list of all queries](#queries-overview) performed on your database in the last 24 hours. For further analysis, you can sort these by metrics like amount of rows read, time per query, and more.

With this built-in tool, you can easily diagnose issues with your queries, allowing you to optimize individual queries without much digging. We will also alert you of any active issues your database may be having in the [Anomalies](anomalies.md) page. This feature flags queries that are running significantly slower than expected.

<YouTubeEmbed id="OAPHvq51hWU" title="Fix Database Faults with PlanetScale Insights" />

## Insights page overview

To view Insights for your database, head to the [PlanetScale dashboard](https://app.planetscale.com), select your database, and click the "**Insights**" tab.

The dropdown on the top left lets you select which branch you want to analyze. You can also choose which servers you want to view insights for: primary or replicas.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/monitoring/query-insights-overview.png?fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=95790fa60341d086b21ba14466419680" alt="PlanetScale Insights overview page" width="2476" height="1652" data-path="postgres/monitoring/query-insights-overview.png" />
</Frame>

You can click the dates listed above the graph to scroll through the past seven days. To further narrow down query analysis, you can select a time range by clicking on the graph and dragging the cursor across. This will zoom in on the selected timeframe.

You also have the option to save a screenshot of the graph by clicking "Save".

### Queries overview table

The table underneath the graph shows all queries performed on your database in the selected timeframe (last 24 hours by default).

For more information about how to read and interpret this data, see the [Queries overview](#queries-overview) section.

### Insights graphs

Once you have selected the branch and server you want to analyze, you can begin exploring the insights for them in the following sections:

<Columns cols={2}>
  <Card title="Query latency" icon="code" horizontal href="#query-latency" />

  <Card title="Queries" icon="code" horizontal href="#queries" />

  <Card title="Rows read" icon="code" horizontal href="#rows-read" />

  <Card title="Rows written" icon="code" horizontal href="#rows-written" />

  <Card title="Any query metric" icon="chart-line" horizontal href="#any-query-metric" />
</Columns>

The remaining sections of this doc walk through how to interpret and act on the data in each graph. If you'd like to see a practical example of how to use Insights to debug a performance issue, check out our [Problem solving with PlanetScale Insights blog post](https://planetscale.com/blog/problem-solving-with-insights) or [this YouTube video](https://www.youtube.com/watch?v=OAPHvq51hWU) walking you through an example.

### Database Traffic Control

[Database Traffic Control](../traffic-control.md) also lives within the Insights tab. It lets you define resource budgets that limit how much CPU, I/O, or connection time specific queries or workloads can consume. You can create budgets directly from query patterns surfaced in Insights, making it easy to put guardrails around problematic queries. See the [Database Traffic Control documentation](../traffic-control.md) for full details.

### MCP server access

Query Insights data is also available through the [PlanetScale MCP server](../../connect/mcp.md). This lets MCP-compatible tools like Cursor, Claude, and VS Code query your Insights data directly — useful for analyzing slow queries, spotting pattern changes, or getting index recommendations without leaving your editor. See the [MCP server documentation](../../connect/mcp.md) for setup instructions.

## Query latency

The default graph depicts your database's query latency in milliseconds over the last 24 hours.

By default, the graph contains two line charts showing `p50` and `p95` latency. This means 50% and 95% of requests, respectively, completed faster than the time listed. You can also click on the `p99` and `p99.9` pills to toggle those on, or click `p50` or `p95` to toggle those off.

## Queries

The Queries graph displays insights about all active running queries in your database. The graph displays total queries per second against the specified time period.

## Rows read

The Rows read graph displays the total number of rows read per second across the selected time period.

## Rows written

The Rows written graph displays the total number of rows written per second across the selected time period.

## Any query metric

The last graph tab plots any single query metric you choose across the selected time period. This tab is named after the metric you have selected, so it reads "Total time" or "Bytes returned" rather than a fixed title.

To graph a metric:

1. Click the chevron on the last graph tab to open the metric list.
2. Select a metric. The graph redraws with that metric plotted over the time period you already have selected.

The list offers the same metrics you can add as columns in the queries table, described in [Available query statistics](#available-query-statistics). It includes only the metrics your branch collects, so the options vary from branch to branch.

Sparklines in the queries table lead to the same graphs. Clicking a sparkline opens that query pattern's [deep dive](#query-deep-dive) with the matching metric already selected, which takes you from every query on the database to one query pattern in a single click.

## Queries overview

The table underneath the graph shows queries performed on your database in the selected timeframe (last 24 hours by default).

<Note>
  The queries table does not show following statements types: `BEGIN`, `COMMIT`, `RELEASE`, `ROLLBACK`, `SAVEPOINT`, `SAVEPOINT_ROLLBACK`, `SET`.
</Note>

Queries are listed with literals replaced by ordinal placeholder values (e.g. \$1). Normalizing queries in this way allows them to be grouped together into patterns, irrespective of the specific parameters used in the underlying query.

You may also see one or more orange icons next to some queries.

* An exclamation point icon indicates that the query is not currently using an index and requires a full table scan.

Hovering over the icon will show a tooltip with information about the meaning of the icon.

This query overviews table shows the same data for all graphs on this page. For information about [Anomalies](anomalies.md) and [Errors](errors.md), refer to those pages.

### Available query statistics

You can customize the metrics that show up on the Queries list by selecting columns in the "View options" dropdown.

| Statistic                    | Description                                                                                                                                                                                                                                                                            |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Query**                    | The query that was run.                                                                                                                                                                                                                                                                |
| **Schema**                   | The default database and schema associated with the connection that issued the query, in the format `database.schema`.                                                                                                                                                                 |
| **Table schema**             | The schema(s) associated with the tables referenced in the query, in the format `database.schema`. (This may differ from the connection schema.)                                                                                                                                       |
| **Qualified table**          | The table(s) referenced in the query, in the format `database.schema.table_name`.                                                                                                                                                                                                      |
| **Table**                    | The table(s) being queried or modified.                                                                                                                                                                                                                                                |
| **% of runtime**             | The percent of the total runtime the query pattern is responsible for (query pattern time divided by the cumulative time of all query patterns on your database).                                                                                                                      |
| **% of CPU time**            | The percent of the total CPU time the query pattern is responsible for (query pattern CPU time divided by the cumulative CPU time of all query patterns on your database).                                                                                                             |
| **% of IO time**             | The percent of the total I/O time the query pattern is responsible for (query pattern I/O time divided by the cumulative I/O time of all query patterns on your database). This column is only present if `track_io_timing` parameter is set in your database's cluster configuration. |
| **Count**                    | The number of times this query has run.                                                                                                                                                                                                                                                |
| **Total time**               | The total time the query has run, in seconds.                                                                                                                                                                                                                                          |
| **CPU time**                 | The cumulative CPU time the query has consumed, in seconds.                                                                                                                                                                                                                            |
| **I/O time**                 | The cumulative I/O time the query has consumed, in seconds. This column is only present if `track_io_timing` parameter is set in your database's cluster configuration.                                                                                                                |
| **p50 latency**              | The p50 latency for the query in milliseconds. This means that 50% of requests completed faster than the time listed.                                                                                                                                                                  |
| **p99 latency**              | The p99 latency for the query in milliseconds. This means that 99% of requests completed faster than the time listed.                                                                                                                                                                  |
| **Max latency**              | The maximum observed latency for the query in milliseconds.                                                                                                                                                                                                                            |
| **Rows read**                | The total number of rows read. This includes all times the query has run in the displayed time frame.                                                                                                                                                                                  |
| **Rows read per query**      | The average number of rows read per execution of the query.                                                                                                                                                                                                                            |
| **Rows returned**            | The total number of rows fetched by a `SELECT` statement. This includes all times the query has run in the displayed time frame.                                                                                                                                                       |
| **Rows returned per query**  | The average number of rows returned per execution of the query.                                                                                                                                                                                                                        |
| **Rows affected**            | The total number of rows modified by an `INSERT`, `UPDATE`, or `DELETE` statement. This includes all times the query has run in the displayed time frame.                                                                                                                              |
| **Rows affected per query**  | The average number of rows affected per execution of the query.                                                                                                                                                                                                                        |
| **Rows read / returned**     | The result of dividing total rows read by rows returned in a query. A high number can indicate that your database is reading unnecessary rows, and the query may be improved by adding an index.                                                                                       |
| **Block cache hit ratio**    | The percentage of blocks read from the shared buffers cache for this query during its execution, avoiding more costly disk reads.                                                                                                                                                      |
| **Blocks hit**               | The total number of blocks read from the shared buffers cache when executing this query.                                                                                                                                                                                               |
| **Blocks read**              | The total number of blocks read from disk when executing this query.                                                                                                                                                                                                                   |
| **Blocks dirtied**           | The total number of blocks modified (but not necessarily flushed to disk) during query execution.                                                                                                                                                                                      |
| **Blocks written**           | The total number of blocks written to disk during query execution.                                                                                                                                                                                                                     |
| **Bytes returned**           | The total number of bytes returned to clients in query responses.                                                                                                                                                                                                                      |
| **Bytes returned per query** | The total number of bytes returned to clients in query responses divided by the number of queries.                                                                                                                                                                                     |
| **Last run**                 | The last time a query was run.                                                                                                                                                                                                                                                         |

You can also sort the columns for quick analysis by clicking on the title at the top of each column.

If `Show sparklines` is selected, numeric columns in the queries table show a time series graph of the value within the selected time period. Click a sparkline to open that query pattern's [deep dive](#query-deep-dive) with the same metric graphed full size.

#### Enabling I/O columns

The **% of I/O** and **I/O time** columns require the `track_io_timing` PostgreSQL config setting to be set to 'on'. This setting can be changed in the "Parameters" tab of the datatbase's cluster configuration. Note that we only begin collection I/O query performance after `track_io_timing` is enabled. Enabling `track_io_timing` may impact query performance.

### Query filtering

The search bar above the table allows you to filter queries as needed. You can filter for query SQL, schema (connection schema, and/or schema of tables referenced by the query), table name, query count, query latency, index name, and if the query was indexed. Click on the `?` next to the search bar for the full list of search syntax.

### Query deep dive

Clicking on a query in the Queries list will open a new page with more information about that query.

You'll first see the full query pattern, which displays the query with data normalized away. This query may run several times with different values, which Insights combines into a single query pattern.

You can display an LLM-generated summary of the query by clicking "Summarize query."

#### Additional query information

Beneath the query pattern is a graph with more information about the query. The set of available metrics/tabs include: Query latency, Queries, Rows read, Rows written, Errors and Indexes, along with a final tab that graphs [any query metric](#any-query-metric) you select. The Indexes graph (which is not shown on the database-level page) shows the percentage of queries that used each of the listed indexes in each time bucket.

Beneath the time series graphs you will see summary statistics for the query pattern. These data are scoped to the same time period shown in the main query pattern graphs. The available metrics have the same definitions as the query statistics listed in the main insights page.

Queries that use an index include a horizontal bar graph that shows the cumulative usage of each index over the complete time period shown in the main query pattern graphs.

To change the time period reflected in the graphs and summary statistics, click and drag to restrict the time window, or click on one of the day icons above the graph to select a different day.

#### Notable queries

Underneath the graph, you'll see a table with more information about notable instances of the query, which are defined as queries that took longer than 1s, read more than 10,000 rows, or produced an error.

If any of the selected queries have [query tags](query-tags.md) attached, you'll see the key-value pairs in the table under `Tags`. See [Query tags](query-tags.md) for how to add tags to your queries.

The table also surfaces when the query started, rows returned, rows read, rows affected, the time it took the query to run (in ms), and the user associated with the query.

## Extension configuration

The `pginsights` extension is responsible for sending query telemetry to the PlanetScale Insights pipeline. Its parameters, including raw query collection and schema name normalization, can be changed in the Extensions tab on your database's Clusters page.

For full parameter documentation, see the [pginsights extension reference](../extensions/pginsights.md#parameters).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
