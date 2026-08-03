---
url: https://planetscale.com/docs/postgres/troubleshooting/out-of-memory
title: "Out Of Memory"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Out of memory (OOM)

> Understanding and resolving out of memory events on PlanetScale Postgres clusters.

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

<PlatformAvailability current="postgres" />

An out of memory (OOM) event occurs when Postgres processes consume more memory than is available on the cluster. When this happens, the impacted instance is automatically restarted to free up memory.

When an OOM happens, we email organization admins. At most one email is sent per database branch per day, even if multiple OOMs happen.

## Impact and risks

When an OOM event occurs:

* **Database restart required** — The impacted instance must be restarted to recover from the OOM event.
* **Downtime** — The instance that OOMed will be unavailable while it restarts. If the primary instance OOMs continuously, an automatic failover will be made.
* **Connections terminated** — Direct connections and connections through the local PgBouncer are dropped during the restart, since the local PgBouncer runs on the same node as Postgres. See the [connection resilience guide](../connection-resilience.md) for details.
* **Transactions rolled back** — Any in-progress transactions that were not committed will be rolled back.

## Primary resolution: Upgrade cluster size

The most direct solution to OOM events is to increase your cluster size to provide more memory. Each cluster size includes a defined amount of memory. See [cluster pricing](../pricing.md#cluster-pricing) for available sizes and their specifications.

Resizing is performed with minimal downtime. See [cluster configuration](../cluster-configuration.md) for more details.

## Understanding memory usage

High memory usage does not necessarily indicate a problem. Whether memory usage is concerning depends on the *type* of memory being used.

The [Metrics](../monitoring/metrics.md) page includes a memory chart that breaks down the different types of memory your cluster is using. Understanding these categories helps you determine if action is needed:

* **Active cache** — Memory used to cache frequently accessed table and index data. This is *good* memory usage — it improves query performance by reducing disk reads. The operating system manages this cache automatically and will release it when needed. High active cache usage does not increase OOM risk.

* **Inactive cache** — Memory used to cache data that hasn't been accessed recently. Like active cache, this memory is managed by the operating system and released when needed. High inactive cache usage does not increase OOM risk.

* **RSS (Resident Set Size)** — Memory actively used by Postgres processes, including connection overhead and query working memory. This memory cannot be reclaimed while processes are running. High RSS usage, especially when combined with many connections, can lead to OOM events.

* **Memory mapped** — Memory mapped files used by Postgres. This is typically stable and managed by the system.

When evaluating memory usage, focus on RSS rather than cache memory. If your cluster shows high total memory usage but most of it is active or inactive cache, the cluster is healthy and performing optimally. The cache will be released automatically if Postgres needs the memory for other operations.

## Common causes of high memory usage

### Too many connections

Postgres uses a connection-per-process architecture. Each connection made to a Postgres server [spawns a new process](https://planetscale.com/blog/processes-and-threads), which consumes system resources including memory and CPU. While the base overhead per connection is modest (a few megabytes), the real memory risk comes from `work_mem` allocations — each query operation (sorts, joins, aggregations) can allocate up to `work_mem` of memory, and a single complex query may perform multiple such operations simultaneously.

If your application opens many connections — either intentionally or due to connection leaks — memory can be exhausted quickly, especially on smaller cluster sizes.

**Recommendations:**

* **Use connection pooling** — [PgBouncer](../connecting/pgbouncer.md) maintains a small pool of connections to Postgres while accepting many client connections, significantly reducing memory usage. All PlanetScale Postgres databases include a local PgBouncer on port `6432`.
* **Monitor active connections** — Use the [Metrics](../monitoring/metrics.md) dashboard to track connection counts.
* **Review application connection handling** — Ensure your application properly closes connections and uses connection pooling libraries.
* **Configure connection limits** — Set appropriate values for `max_connections` in your [Postgres parameters](../cluster-configuration/parameters.md).

### Memory-intensive queries

Certain query patterns can consume large amounts of memory:

| Query Pattern                      | Why It Uses Memory                                                          |
| ---------------------------------- | --------------------------------------------------------------------------- |
| Large `ORDER BY` operations        | Sorts require memory to hold and reorder rows                               |
| `GROUP BY` on large datasets       | Aggregations build hash tables in memory                                    |
| `DISTINCT` on large result sets    | Requires storing unique values for deduplication                            |
| Hash joins on large tables         | Hash tables for join keys are built in memory                               |
| CTEs (Common Table Expressions)    | May materialize entire result sets in memory                                |
| Large `IN` clauses                 | Arrays and lists are stored in memory                                       |
| `array_agg()` or similar functions | Accumulates data in memory                                                  |
| JSON operations on large documents | Parsing and manipulating JSON requires loading entire documents into memory |

**Recommendations:**

* **Add appropriate indexes** — Indexes can eliminate the need for in-memory sorts by returning data in the correct order.
* **Use `LIMIT`** — Restrict result set sizes when you don't need all rows.
* **Break large operations into batches** — Process data in smaller chunks rather than all at once.
* **Optimize query plans** — Use `EXPLAIN (ANALYZE, VERBOSE, BUFFERS, MEMORY)` to understand how queries use memory.
* **Monitor query performance** — Use [Query Insights](../monitoring/query-insights.md) to identify memory-heavy queries.

### Postgres memory configuration

Several Postgres parameters control memory allocation:

| Parameter              | Description                                                                                                                                                                                                          |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `work_mem`             | Memory allocated per operation for sorts, hash tables, and similar operations. A single query with multiple operations can allocate this amount multiple times, potentially using several times `work_mem` in total. |
| `maintenance_work_mem` | Memory for maintenance operations like `VACUUM`, `CREATE INDEX`, and `ALTER TABLE`.                                                                                                                                  |
| `shared_buffers`       | Memory for caching table and index data.                                                                                                                                                                             |

These parameters are configured automatically based on your cluster size, but custom configurations via [Postgres parameters](../cluster-configuration/parameters.md) may need adjustment if you experience OOM events.

<Warning>
  Increasing `work_mem` too high can cause OOM events if many queries run concurrently, as each query operation can allocate up to this amount.
</Warning>

## Monitoring and prevention

To prevent OOM events:

1. **Monitor memory usage** — Use the [Metrics](../monitoring/metrics.md) dashboard to track memory utilization over time.
2. **Set up alerts** — Use external monitoring tools to alert when memory usage exceeds thresholds. See [Prometheus integration](../monitoring/prometheus-postgres.md) for scraping metrics into your monitoring system.
3. **Review connection counts** — High connection counts often correlate with memory pressure.
4. **Analyze slow queries** — Memory-intensive queries often appear in slow query logs. Use [Query Insights](../monitoring/query-insights.md) to identify them.
5. **Right-size your cluster** — Ensure your cluster has adequate memory for your workload, with headroom for traffic spikes.

<Note>
  Some queries can spike memory usage so quickly that the increase is not captured in the metrics graph. If you see OOM annotations on the graph but no corresponding memory spike, the OOM did occur — the memory increase simply happened too fast to be recorded. In these cases, review [Query Insights](../monitoring/query-insights.md) to identify queries that ran around the time of the OOM event.
</Note>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
