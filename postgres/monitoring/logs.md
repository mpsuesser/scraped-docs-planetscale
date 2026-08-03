---
url: https://planetscale.com/docs/postgres/monitoring/logs
title: "Logs"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Dashboard overview

The Logs dashboard displays real-time and historical log entries from your database cluster across all servers. You can filter and analyze logs by:

- **Server Filter**: Monitor all servers or focus on specific replica instances
- **Log Level**: Filter by severity (DEBUG, INFO, WARNING, ERROR)
- **Time Range**: View logs from the past hour up to custom time ranges
- **Branch**: Select which database branch to monitor
- **Search**: Full-text search across log messages
- **Live update**: Toggle on/off the auto-refresh of data every ~10 seconds

## Log filtering and navigation

### Server selection

The server filter dropdown allows you to choose which database instances to view logs from. You can select the Primary, Replicas, or all servers. By default just the Primary is shown:

| Filter Option | Purpose |
| --- | --- |
| **Primary** | Monitor main database server logs |
| **Replicas** | Monitor individual replica servers |

### Log level filtering

By default all log levels are displayed. You can filter them based on the following log levels:

| Level | Color Code | Purpose |
| --- | --- | --- |
| **DEBUG** | Gray | Detailed system information |
| **INFO** | Blue | General information messages |
| **WARNING** | Yellow | Potential issues requiring attention |
| **ERROR** | Red | Critical issues requiring immediate action |

### Time range selection

The time range selector offers several preset options:

- **Past 15 minutes**
- **Past hour**
- **Past 3 hours**
- **Past 6 hours**
- **Past 12 hours**
- **Past day**

**Custom range**: Set specific date and time ranges for targeted analysis

All log timestamps are displayed in the timezone configured in the top right of the console under the drop down ‘Timezone display’

## Log retention

By default, logs are retained for **7 days**. This retention period ensures you have access to recent log history for troubleshooting and analysis while managing storage efficiently.

## Search and filtering

### Full-text search

Use the search bar to find specific:

- Error messages and stack traces
- Connection identifiers
- Query patterns
- Configuration changes

### LogQL search syntax

PlanetScale’s logging capabilities utilize Victoria Logs, which supports powerful LogQL query syntax. Click the `?` icon in the search box to see syntax help. Common examples include:

**Text search:**

- `error` - Find logs containing “error”
- `"received message"` - Exact phrase search

**Field filtering:**

- `planetscale.pod:h2l-pod` - Filter by specific pod
- `planetscale.role:replica` - Filter by database role
- `planetscale.container:pgbouncer` - Filter by container type
- `planetscale.availability_zone:us-east-1a` - Filter by availability zone

**Exclusion filters:**

- `NOT error` - Exclude logs containing “error”
- `NOT "received message"` - Exclude specific phrases

**Logical operators:**

- `"received message" OR planetscale.container:pgbouncer` - Match either condition

For complete LogQL syntax documentation, see: [Victoria Logs LogQL Documentation](https://docs.victoriametrics.com/victorialogs/logsql/)

### Advanced filtering

Combine multiple filters for precise log analysis and advanced log discovery:

- **Server type + Log level**: Filter Primary/Replica servers by ERROR/WARNING levels for targeted troubleshooting
- **Time window + Search terms**: Narrow down to specific time ranges with LogQL queries for incident investigation
- **Log level + Search bar**: Combine severity filtering with text search for comprehensive debugging
- **Server selection + Time range + LogQL**: Use all filtering options together for precise log discovery

## Log message structure

When you click on a log entry, a detailed view appears on the right side showing:

| Field | Description | Example |
| --- | --- | --- |
| **Time** | Exact timestamp of the log event with timezone | `2025-06-23 17:31:27 UTC` |
| **Level** | Log severity level with color coding | `INFO`, `DEBUG`, `ERROR`, `WARNING` |
| **Role** | Database server role | `primary` |
| **Pod** | Kubernetes pod identifier | `h2l-8abod1fll1tn-aws-useast1b-1-1335211508-467a6a97` |
| **Container** | Container identifier | `postgres` |
| **Availability Zone** | Geographic server location | `us-east-1a` |
| **Message** | Full log message content | Complete error messages, SQL queries, system events |

The Logs dashboard serves as your primary debugging and monitoring tool, providing the detailed visibility needed to maintain optimal database performance and quickly resolve issues.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
