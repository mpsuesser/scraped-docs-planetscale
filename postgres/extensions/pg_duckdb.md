---
url: https://planetscale.com/docs/postgres/extensions/pg_duckdb
title: "Pg_duckdb"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Extensions: pg_duckdb

> pg_duckdb is a Postgres extension that embeds DuckDB, a high-performance analytical database, directly into Postgres.

This page covers Postgres.

> [!WARNING]
>
> We don't recommend running `pg_duckdb` directly on your PlanetScale Postgres database as it can consume significant resources during analytical queries. If you want to use DuckDB for analytics, we recommend using [MotherDuck](https://motherduck.com/) to host your analytical workloads separately.

## Dashboard Configuration

This extension requires activation via the PlanetScale dashboard before it can be used. It must be enabled through shared libraries and requires a database restart.

To enable pg\_duckdb:

**Step 1**

From the PlanetScale organization dashboard, select the desired database

**Step 2**

Navigate to the **Clusters** page from the menu on the left

**Step 3**

Choose the branch whose extensions you'd like to configure in the "**Branch**" dropdown

**Step 4**

Select the **Extensions** tab

**Step 5**

Enable `pg_duckdb` and configure its parameters

**Step 6**

Click **Queue extension changes** to apply the configuration

**Step 7**

Once you're ready to apply the changes, click "**Apply changes**"

## Parameters

### duckdb.postgres\_role

* **Type**: String
* **Default**: `pscale_superuser`
* **Description**: Specifies the Postgres role that is allowed to use DuckDB execution and manage secrets.

### duckdb.memory\_limit

* **Type**: Integer
* **Default**: 0
* **Description**: Maximum memory DuckDB can use per connection in megabytes. Setting to 0 activates DuckDB's default (80% of available RAM).

## Usage

After enabling the extension through the dashboard, you can install it in your database:

```sql theme={null}
CREATE EXTENSION IF NOT EXISTS pg_duckdb;
```

Once installed, you can use DuckDB's analytical capabilities directly from PostgreSQL. For example:

```sql theme={null}
-- Query PostgreSQL tables using DuckDB's analytical engine
SELECT * FROM duckdb.read_parquet('s3://bucket/data.parquet');
```

## External Documentation

For more detailed information about `pg_duckdb` usage and functionality, see the [official `pg_duckdb` documentation](https://github.com/duckdb/pg_duckdb).


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
