---
url: https://planetscale.com/docs/postgres/extensions/pg_partman_bgw
title: "Pg_partman_bgw"
description: ""
access_date: 2026-10-20T18:01:41.586Z
current_date: 2026-10-20T18:01:41.586Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Extensions: pg_partman_bgw

> The pg_partman_bgw extension is a background worker for managing partitioned tables in PostgreSQL. It automates the creation and maintenance of partitioned tables, making it easier to manage large datasets.

## Dashboard Configuration

This extension requires activation via the PlanetScale Dashboard before it can be used. It must be enabled through shared libraries and requires a database restart.

To enable pg\_partman\_bgw:

**Step 1**

From the PlanetScale organization dashboard, select the desired database

**Step 2**

Navigate to the **Clusters** page from the menu on the left

**Step 3**

Choose the branch whose extensions you'd like to configure in the "**Branch**" dropdown

**Step 4**

Select the **Extensions** tab

**Step 5**

Enable pg\_partman\_bgw and configure its parameters

**Step 6**

Click **Queue extension changes** to apply the configuration

**Step 7**

Once you're ready to apply the changes, click "**Apply changes**"

## Parameters

### pg\_partman\_bgw\.interval

* **Type**: Integer
* **Default**: `3600`
* **Description**: Interval (in seconds) between partition maintenance runs.

### pg\_partman\_bgw\.dbname

* **Type**: String
* **Default**: `postgres`
* **Description**: Database name where the background worker should connect.

### pg\_partman\_bgw\.role

* **Type**: String
* **Default**: `postgres`
* **Description**: Role that the background worker should use when connecting.

### pg\_partman\_bgw\.analyze

* **Type**: Boolean
* **Default**: `false`
* **Description**: Whether to run ANALYZE on newly created partitions.

### pg\_partman\_bgw\.jobmon

* **Type**: Boolean
* **Default**: `true`
* **Description**: Enable job monitoring for partition maintenance activities.

## Usage

After enabling the extension through the dashboard, the background worker will automatically run partition maintenance based on your pg\_partman configuration. You'll also need to install the pg\_partman extension:

```sql theme={null}
CREATE EXTENSION IF NOT EXISTS pg_partman;
```

The background worker works in conjunction with pg\_partman to automatically maintain your partitioned tables.

## External Documentation

For more detailed information about pg\_partman and pg\_partman\_bgw usage, see the [official pg\_partman documentation](https://github.com/pgpartman/pg_partman).


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
