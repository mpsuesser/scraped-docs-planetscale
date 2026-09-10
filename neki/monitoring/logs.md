---
url: https://planetscale.com/docs/neki/monitoring/logs
title: "Logs"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Logs

> Search and filter Neki logs by branch, shard, server, level, and time.

The Logs page displays log events from the routers and Postgres instances
running a Neki database branch. Use its filters and search syntax to find events
that occurred during a selected time range.

To open the page, select a database and branch in the PlanetScale dashboard,
then select **Logs**.

## Filter logs

The Logs page provides the following controls:

| Control        | Behavior                                                                                                                                   |
| :------------- | :----------------------------------------------------------------------------------------------------------------------------------------- |
| **Branch**     | Select the database branch whose logs you want to inspect.                                                                                 |
| **Shards**     | Show events from one or more Neki shards.                                                                                                  |
| **Servers**    | Select individual servers, grouped by role. By default, logs from every shard primary are shown.                                           |
| **Levels**     | Filter by `DEBUG`, `INFO`, `WARNING`, or `ERROR`. With no specific levels selected, all levels are shown.                                  |
| **Time range** | Select a preset or custom range. The custom date picker is limited to the previous seven days, and the default range is the previous hour. |
| **Live**       | Refresh the results approximately every 10 seconds. Live mode is enabled by default.                                                       |
| **Refresh**    | Request the latest matching events immediately.                                                                                            |
| **Download**   | Download the results on the current page as CSV or JSON, or copy them as JSON when clipboard access is available.                          |

Selecting shards narrows the server menu to the servers in those shards. Neki
routers are represented separately from shards, so selecting fewer than all
shards removes routers from the server menu. To select routers, leave the shard
filter set to all shards.

Use the shard and server filters together when an issue appears to affect one
shard or one primary or replica.

The Logs page returns up to 100 events per page. Use **Previous** and **Next** to
move through additional results.

## Search logs

Use the search field to find text or filter structured log fields. Select the
question-mark icon beside the search field to open the syntax reference.

### Search for text

Enter a word to find messages containing it:

```text theme={null}
error
```

Use quotation marks to search for an exact phrase:

```text theme={null}
"Received message"
```

Exclude matching messages with `NOT`:

```text theme={null}
NOT "Received message"
```

Use `OR` when either expression can match:

```text theme={null}
"Received message" OR planetscale.container:postgres
```

### Search structured fields

Log entries contain structured fields that can be searched directly:

| Field             | Example                                    |
| :---------------- | :----------------------------------------- |
| Pod               | `planetscale.pod:hzi-pod`                  |
| Role              | `planetscale.role:replica`                 |
| Container         | `planetscale.container:postgres`           |
| Availability zone | `planetscale.availability_zone:us-east-1a` |

You can combine text, field filters, level, shard, server, and time controls
to narrow an investigation.

PlanetScale log search uses
[VictoriaLogs LogsQL](https://docs.victoriametrics.com/victorialogs/logsql/).
The search help in the dashboard shows the syntax supported by the Logs page.

## Read a log entry

The results table shows each event's time, level, and message. Enable or disable
**Single line** to control whether each message is displayed on one line in the
table.

Select an event to open its details panel. Depending on the event, the panel
shows:

* Timestamp and configured display timezone.
* Log level.
* Server role, when present.
* Pod.
* Neki shard.
* Container.
* Availability Zone.
* Complete raw message.

The shard field shows the shard identifier and, when available, its
customer-facing display name.

Use the controls on a structured field to copy its value or add it to the
current search. You can also copy the complete raw message and move to the
previous or next event without closing the panel.

## Export log results

The download menu operates on the results currently loaded on the page:

* **Download as CSV** exports timestamp, level, message, role, container,
  availability zone, and pod fields.
* **Download as JSON** exports the same results as structured JSON.
* **Copy JSON to clipboard** copies the current results when clipboard access is
  available.

Narrow the shard, server, time, and search filters before exporting if you need
a focused set of events.

## Investigate an incident

When investigating a performance or availability problem:

1. Match the Logs time range to the affected period in
   [Metrics](metrics.md) or
   [Query Insights](query-insights.md).
2. Select the affected shards and servers.
3. Start with `ERROR` and `WARNING`, then include the other levels for more
   context.
4. Open a relevant event and filter by its shard, pod, role, or container.
5. Compare events across instances to determine whether the problem is isolated
   or branch-wide.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
