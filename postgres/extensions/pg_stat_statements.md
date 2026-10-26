---
url: https://planetscale.com/docs/postgres/extensions/pg_stat_statements
title: "Pg_stat_statements"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Extensions: pg_stat_statements

> pg_stat_statements is a PostgreSQL extension that tracks execution statistics of all SQL statements executed by a server. It provides insights into query performance, allowing you to identify slow queries and optimize them

This page covers Postgres.

## Dashboard Configuration

This extension requires activation via the PlanetScale Dashboard before it can be used. It must be enabled through shared libraries and requires a database restart.

To enable pg\_stat\_statements:

**Step 1**

From the PlanetScale organization dashboard, select the desired database

**Step 2**

Navigate to the **Clusters** page from the menu on the left

**Step 3**

Choose the branch whose extensions you'd like to configure in the "**Branch**" dropdown

**Step 4**

Select the **Extensions** tab

**Step 5**

Enable pg\_stat\_statements and configure its parameters

**Step 6**

Click **Queue extension changes** to apply the configuration

**Step 7**

Once you're ready to apply the changes, click "**Apply changes**"

## Parameters

### pg\_stat\_statements.max

* **Type**: Integer
* **Default**: `5000`
* **Description**: Maximum number of statements tracked by the extension.

### pg\_stat\_statements.track

* **Type**: Select
* **Options**: top, all
* **Default**: `top`
* **Description**: Which statements to track. 'top' tracks top-level statements only, 'all' tracks all statements including nested ones.

### pg\_stat\_statements.track\_utility

* **Type**: Boolean
* **Default**: `true`
* **Description**: Whether to track utility statements (such as PREPARE, EXPLAIN, etc.).

### pg\_stat\_statements.track\_planning

* **Type**: Boolean
* **Default**: `false`
* **Description**: Whether to track planning statistics for statements.

### pg\_stat\_statements.save

* **Type**: Boolean
* **Default**: `true`
* **Description**: Whether to save statement statistics across server restarts.

## Usage

After enabling the extension through the dashboard, you can install it in your database:

```sql theme={null}
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
```

Then you can query the statistics view:

```sql theme={null}
-- View the most time-consuming queries
SELECT
    query,
    calls,
    total_exec_time,
    mean_exec_time,
    rows
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 10;
```

## External Documentation

For more detailed information about pg\_stat\_statements usage and the available statistics, see the [official PostgreSQL documentation](https://www.postgresql.org/docs/current/pgstatstatements.html).


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
