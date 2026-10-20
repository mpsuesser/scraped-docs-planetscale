---
url: https://planetscale.com/docs/postgres/extensions/pg_squeeze
title: "Pg_squeeze"
description: ""
access_date: 2026-10-20T18:01:41.586Z
current_date: 2026-10-20T18:01:41.586Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Extensions: pg_squeeze

> pg_squeeze automatically cleans up unused space in tables (table bloat). pg_squeeze helps maintain optimal table performance by removing dead space that accumulates over time from UPDATE and DELETE operations.

## Dashboard Configuration

This extension requires activation via the PlanetScale Dashboard before it can be used. It must be enabled through shared libraries and requires a database restart.

To enable pg\_squeeze:

**Step 1**

From the PlanetScale organization dashboard, select the desired database

**Step 2**

Navigate to the **Clusters** page from the menu on the left

**Step 3**

Choose the branch whose extensions you'd like to configure in the "**Branch**" dropdown

**Step 4**

Select the **Extensions** tab

**Step 5**

Enable pg\_squeeze and configure its parameters

**Step 6**

Click **Queue extension changes** to apply the configuration

**Step 7**

Once you're ready to apply the changes, click "**Apply changes**"

## Parameters

### squeeze.max\_xlock\_time

* **Type**: Integer (milliseconds)
* **Default**: `0`
* **Minimum**: `0`
* **Description**: The maximum time the processed table may be locked exclusively.

### squeeze.worker\_autostart

* **Type**: String
* **Default**: `postgres`
* **Description**: Space-separated list of databases to start background workers for automatically.

### squeeze.workers\_per\_database

* **Type**: Integer
* **Default**: `1`
* **Minimum**: `1`
* **Description**: Maximum number of worker processes launched per database. Must be less than or equal to cluster-level max\_worker\_processes.

## Usage

After enabling the extension through the dashboard, you can install it in your database:

```sql theme={null}
CREATE EXTENSION IF NOT EXISTS pg_squeeze;
```

Then you can schedule tables for squeezing:

```sql theme={null}
-- Schedule a table to be squeezed
SELECT squeeze.squeeze_table('public', 'your_table_name');
```

> [!NOTE]
>
> Only the [default role](../connecting/roles.md#default-role) has the permissions necessary to start a squeeze process.

## External Documentation

For more detailed information about pg\_squeeze usage and configuration, see the [official pg\_squeeze documentation](https://github.com/cybertec-postgresql/pg_squeeze/).


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
