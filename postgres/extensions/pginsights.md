---
url: https://planetscale.com/docs/postgres/extensions/pginsights
title: "Pginsights"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Dashboard Configuration

This extension is always enabled for PlanetScale databases and requires activation via the PlanetScale Dashboard. While the extension itself is always active, some of its parameters can be configured through the dashboard.

To configure pginsights parameters:

## Parameters

### Raw query collection

- **Setting:** `pginsights.raw_queries`
- **Type:** Boolean
- **Default:** `false`

When enabled, Insights collects the full query text for notable queries, including all literal values. When disabled, only the normalized SQL, with literals removed, will be collected.

Enabling complete query collection can be helpful when performance varies significantly within the same query pattern, and you need to see the full SQL statement without placeholders to troubleshoot the underlying issue.

Enabling this setting may result in sensitive data that appears in queries being sent to PlanetScale, where it will be processed and stored in accordance with our privacy policy.

### Schema name normalization

- **Setting:** `pginsights.normalize_schema_names`
- **Type:** Boolean
- **Default:** `false`

When enabled, [schema](https://www.postgresql.org/docs/current/ddl-schemas.html) names appearing in queries are normalized as if they were literal values.

Consider the following example query: `select * from myschema.users where id = 1`.

- With `pginsights.normalize_schema_names` set to false, the query will be reported in insights as `select * from myschema.users where id = $1`
- With `pginsights.normalize_schema_names` set to true, the query will be reported in insights as `select * from $1.users where id = $2`

This setting is useful for databases using a schema-per-tenant design, where each user or tenant’s data is stored in an isolated Postgres schema. With this feature enabled, query patterns that are identical except for the namespace will be grouped together, resulting in fewer distinct query patterns and more navigable insights.

### Database Traffic Control shared memory size

- **Setting:** `traffic_control.shm_size`
- **Type:** Integer (bytes)
- **Default:** `64kB`

Controls how much shared memory is allocated to store [Database Traffic Control](../traffic-control.md) resource budgets.

This setting matters most for Traffic Control rules that match each unique value for a tag key. The default `64kB` size tracks about 600 active budget entries at once. The maximum `1MB` size tracks about 10,000 active budget entries.

Active budget entries are stored in an LRU cache. The limit applies to recently active values, not the total number of possible tag values.

## Usage

The pginsights extension is automatically installed and enabled for all PlanetScale databases. You don’t need to manually create the extension - it’s always active and collecting query insights.

The extension integrates with PlanetScale’s Query Insights dashboard to provide:

- Query performance metrics
- Slow query identification
- Query pattern analysis
- Resource usage tracking

## External Documentation

For more information about Query Insights and how to use the collected data, see the [Query Insights documentation](../monitoring/query-insights.md).
