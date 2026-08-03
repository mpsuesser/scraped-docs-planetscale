---
url: https://planetscale.com/docs/postgres/extensions/timescaledb
title: "Timescaledb"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

Only features from the [Apache 2 Edition](https://docs.tigerdata.com/about/latest/timescaledb-editions/) of TimescaleDB are supported.

## Dashboard Configuration

This extension requires activation via the PlanetScale Dashboard before it can be used. It must be enabled through shared libraries and requires a database restart.

To enable TimescaleDB:

## Usage

After enabling the extension through the dashboard, you’ll be able to install it in your Postgres cluster:

```sql
CREATE EXTENSION IF NOT EXISTS timescaledb;
```

## External Documentation

For more detailed information about TimescaleDB usage, see the [official documentation](https://github.com/timescale/timescaledb).

## Parameters

### timescaledb.enable\_chunk\_append

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable chunk append node

### timescaledb.enable\_chunk\_skipping

- **Type**: Boolean
- **Default**: `false`
- **Description**: Enable chunk skipping functionality

### timescaledb.enable\_constraint\_aware\_append

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable constraint-aware append scans

### timescaledb.enable\_constraint\_exclusion

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable constraint exclusion

### timescaledb.enable\_custom\_hashagg

- **Type**: Boolean
- **Default**: `false`
- **Description**: Enable custom hash aggregation

### timescaledb.enable\_deprecation\_warnings

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable warnings when using deprecated functionality

### timescaledb.enable\_event\_triggers

- **Type**: Boolean
- **Default**: `false`
- **Description**: Enable event triggers for chunks creation

### timescaledb.enable\_foreign\_key\_propagation

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable foreign key propagation

### timescaledb.enable\_job\_execution\_logging

- **Type**: Boolean
- **Default**: `false`
- **Description**: Enable job execution logging

### timescaledb.enable\_now\_constify

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable now() constify

### timescaledb.enable\_optimizations

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable TimescaleDB query optimizations

### timescaledb.enable\_ordered\_append

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable ordered append scans

### timescaledb.enable\_parallel\_chunk\_append

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable parallel chunk append node

### timescaledb.enable\_qual\_propagation

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable qualifier propagation

### timescaledb.enable\_runtime\_exclusion

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable runtime chunk exclusion

### timescaledb.enable\_tiered\_reads

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable tiered data reads

### timescaledb.enable\_tss\_callbacks

- **Type**: Boolean
- **Default**: `true`
- **Description**: Enable ts\_stat\_statements callbacks

### timescaledb.max\_cached\_chunks\_per\_hypertable

- **Type**: Integer
- **Default**: `1024`
- **Min**: `0`
- **Max**: `65536`
- **Description**: Maximum cached chunks

### timescaledb.max\_open\_chunks\_per\_insert

- **Type**: Integer
- **Default**: `1024`
- **Min**: `0`
- **Max**: `32767`
- **Description**: Maximum open chunks per insert

### timescaledb.restoring

- **Type**: Boolean
- **Default**: `false`
- **Description**: Enable restoring mode for TimescaleDB
