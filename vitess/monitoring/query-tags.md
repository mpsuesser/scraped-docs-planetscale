---
url: https://planetscale.com/docs/vitess/monitoring/query-tags
title: "Query Tags"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Tags

> Attach key-value metadata to SQL statements to filter performance data in Insights.

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

<PlatformAvailability current="vitess" postgres="/postgres/monitoring/query-tags" />

<Frame>
  <img className="hidden dark:block" src="https://mintcdn.com/planetscale-2/Ez_PNQOyZBhNfDDQ/vitess/monitoring/insights-tags-dark.png?fit=max&auto=format&n=Ez_PNQOyZBhNfDDQ&q=85&s=c92cbce8c054c7f172cfaff2b32ac0af" alt="Insights tags" width="1456" height="1718" data-path="vitess/monitoring/insights-tags-dark.png" />

  <img className="dark:hidden" src="https://mintcdn.com/planetscale-2/Ez_PNQOyZBhNfDDQ/vitess/monitoring/insights-tags-light.png?fit=max&auto=format&n=Ez_PNQOyZBhNfDDQ&q=85&s=fa09e8d424f13817db98b2528dd1fc7f" alt="Insights tags" width="1464" height="1714" data-path="vitess/monitoring/insights-tags-light.png" />
</Frame>

## Overview

Query tags are key-value pairs embedded in SQL comments written into each statement to the database. PlanetScale parses these tags automatically and uses them to show filtered tag-based performance data alongside individual query executions in [Insights](query-insights.md).

Tags follow the [SQLCommenter](https://google.github.io/sqlcommenter/) specification, an open standard created by Google for attaching metadata to SQL statements as comments.

## Tag format

A tag comment is appended to the SQL statement inside `/* */` delimiters. Each tag is a `key='value'` pair, and multiple tags are separated by commas:

```sql theme={null}
SELECT *
FROM orders
WHERE status = 'pending'
/*app='web',route='api-export'*/;
```

This example would create the following tags in the PlanetScale dashboard:

* **app:** `web`
* **route:** `api-export`

Values are URL-encoded and wrapped in single quotes, following the [SQLCommenter specification](https://google.github.io/sqlcommenter/spec/).

<Warning>
  Tags must appear **before** the semicolon that ends the SQL statement. Tags placed after the semicolon are not recognized by PlanetScale.

  ```sql theme={null}
  -- Correct: tag before semicolon
  SELECT 1 /*category='analytics'*/;

  -- Incorrect: tag after semicolon
  SELECT 1; /*category='analytics'*/
  ```

  When a tag appears after the semicolon, the database treats it as a separate statement (a no-op comment), so Insights sees only the original query without any tags.
</Warning>

<Note>
  If you're sending queries with comments using the MySQL shell, make sure you have [enabled comments with the `-c` flag](https://dev.mysql.com/doc/refman/en/mysql-command-options.html#option_mysql_comments).
</Note>

## Where tags appear in PlanetScale

### Insights

Queries in the Insights page can be filtered by tags by using the search bar. For example, to filter for queries with the tag `action='analytics'`, use the following syntax:

```
tag:action:analytics
```

### Insights → Tags

"Tags" is a top-level item under [Insights](query-insights.md). Select one or many key/value pairs to filter the performance data. Or see the tags of all queries within the currently selected time range.

You can also see the tags associated with a query on the query details page (click on the query text from the list in Insights).

### Tag cardinality limits

You can attach tags with any cardinality. When a tag produces too many distinct values, PlanetScale may collapse the value to keep tag data usable in Insights.

Tag values may be collapsed when either of these limits is exceeded:

* Any query pattern has more than 20 values for the tag
* Any query pattern has more than 50 distinct combinations of tag values within a 15-second period

When a tag value is collapsed, PlanetScale removes the original value and reports it as `Collapsed` in the query details page.

If you search for a tag with collapsed values, or open the Tags view for a tag with collapsed values, PlanetScale shows a warning with the percentage of results affected by collapsing.

## Adding tags to your queries

There are two approaches to tagging queries: using an SQLCommenter integration that adds tags automatically, or appending tag comments manually.

### SQLCommenter integrations

The [SQLCommenter project](https://google.github.io/sqlcommenter/) provides middleware and plugins for several frameworks and ORMs that automatically attach tags to every query:

| Language | Supported frameworks / ORMs             |
| -------- | --------------------------------------- |
| Python   | Django, SQLAlchemy, Flask               |
| Java     | Hibernate, Spring                       |
| Node.js  | Knex.js, Sequelize.js, Express.js       |
| Go       | `database/sql`, gorilla/mux, `net/http` |
| PHP      | Laravel                                 |
| Ruby     | Ruby on Rails                           |

[Drizzle](https://orm.drizzle.team/docs/sql-comments) and Prisma support SQLCommenter natively.

These integrations typically inject framework-level context like the route, controller, and framework version. Some support adding custom key-value pairs.

<Note>
  Several popular ORMs and query builders used with PlanetScale do not have official SQLCommenter support, including **Bun** and **Kysely**. If you use one of these, see [manual tagging](#manual-tagging) below.
</Note>

### Manual tagging

If your ORM or query builder does not have an SQLCommenter plugin, you can append the tag comment directly to your SQL statements. The format is straightforward: add `/*key='value'*/` before the semicolon.

#### Raw SQL

```sql theme={null}
SELECT * 
FROM orders
WHERE user_id = ?
/*app='checkout',feature='order-history'*/;
```

#### With a query builder

```typescript theme={null}
// Example using Kysely
const result = await db
  .selectFrom('orders')
  .where('user_id', '=', userId)
  .selectAll()
  .execute({
    // Append the tag comment to the generated SQL
    queryId: "/*app='checkout',feature='order-history'*/"
  });
```

<Note>
  The exact mechanism for appending a comment varies by library. Some query builders support a comment or annotation option, while others require you to modify the SQL string directly. Check your library's documentation for the best approach.
</Note>

#### Encoding rules

When adding tags manually, follow these rules from the SQLCommenter specification:

* **URL-encode** values that contain special characters. For example, `/api/export` becomes `%2Fapi%2Fexport`.
* **Escape** single quotes within values with a backslash: `name='O\'Brien'`.
* **Sort** key-value pairs lexicographically by key if you want to match the specification exactly, though PlanetScale does not require a specific order.

## Troubleshooting

### Tags are not appearing in Insights

The most common cause is **tag placement**. Tags must appear before the semicolon. See the [warning above](#tag-format) for the correct format.

If your tags are correctly placed and still not appearing:

* Verify that your queries are being executed and appear in the [Insights queries table](query-insights.md)
* Check that you are searching with the correct syntax: `tag:key:value` (for example, `tag:category:analytics`)
* If you're using the MySQL shell, confirm you have [enabled comments with the `-c` flag](https://dev.mysql.com/doc/refman/en/mysql-command-options.html#option_mysql_comments)

### ORM is stripping comments

Some ORMs and connection poolers may strip SQL comments before sending queries to the database. If you have added tags but they are not appearing:

* Check whether your ORM has a setting to preserve SQL comments
* If you use a connection pooler, verify that it is not configured to strip comments

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
