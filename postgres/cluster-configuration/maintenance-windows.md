---
url: https://planetscale.com/docs/postgres/cluster-configuration/maintenance-windows
title: "Maintenance Windows"
description: ""
access_date: 2026-10-29T21:39:41.267Z
current_date: 2026-10-29T21:39:41.267Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Configure Postgres maintenance windows

> Choose when PlanetScale restarts your production Postgres branches for maintenance.

This page covers Postgres.

Some maintenance, such as security fixes and Postgres and image updates, requires restarting Postgres. Restarts are brief, and most applications get through them with minimal impact, especially with a [dedicated PgBouncer](https://planetscale.com/docs/postgres/connecting/pgbouncer.md#dedicated-primary-pgbouncers) and [replicas](https://planetscale.com/docs/postgres/scaling/replicas.md). Still, you should decide when they happen. Every production branch has a weekly two-hour maintenance window. Most windows only run routine maintenance, which doesn't restart Postgres, and we email your database administrators at least 7 days before a restart.

## Types of maintenance

| | What it is | Notice | Can you move it? |
| :- | :- | :- | :- |
| **Routine** | Small updates and configuration changes, applied in your window without a restart | None. Routine maintenance appears in your audit log. | No |
| **Restart required** | Updates that restart Postgres, such as Postgres minor versions and image updates | Email and webhook, at least 7 days ahead | Yes, up to 3 times before its deadline |

> [!NOTE]
>
> In rare cases, such as a critical security vulnerability, we may need to restart outside your maintenance window or with less than 7 days' notice.

## Change your maintenance window

Every production branch gets a maintenance window in UTC when it's created, and the dashboard also shows it in your local time zone.

![Settings Maintenance page showing each production branch's weekly maintenance window, with Change schedule and View history](https://mintcdn.com/planetscale-2/ChSZMNGaPLeQQtQn/postgres/cluster-configuration/maintenance-windows-navigation.png?fit=max\&auto=format\&n=ChSZMNGaPLeQQtQn\&q=85\&s=8799aabb40579b0d54100b0f99bc3617)

**Step 1**

From the PlanetScale dashboard, select your database.

**Step 2**

Open **Settings**, then select **Maintenance**.

**Step 3**

Find the production branch and select **Change schedule**.

**Step 4**

Choose the day of the week and the hour in UTC. The window lasts two hours.

**Step 5**

Select **Save**.

Only database administrators can change a maintenance window. Select **View history** to see past windows.

If restart-required maintenance is already scheduled, changing your window moves it to the new window. You can't choose a window that doesn't open before the maintenance's deadline.

## Restart-required maintenance

We email your database administrators and send webhooks at each of the below steps. Each notice includes information about what's changing, why, what to expect, and the deadline.

| Notice | When |
| :- | :- |
| Scheduled | When we schedule it, at least 7 days ahead |
| Reminder | 3 days before |
| Started | When it starts in your window |
| Completed | When it finishes |
| Canceled | If we cancel it |

Maintenance starts within 10 minutes after your window's start time. Most maintenance finishes well within the window, but in rare cases it can run past the end.

### Reschedule maintenance

Each email links to the maintenance event in your dashboard. You can also find scheduled events under **Settings** > **Maintenance**.

![Upcoming maintenance card for a Postgres image update, showing what's changing, the scheduled time, the must-run-by deadline, and the Reschedule button](https://mintcdn.com/planetscale-2/ChSZMNGaPLeQQtQn/postgres/cluster-configuration/maintenance-windows-update.png?fit=max\&auto=format\&n=ChSZMNGaPLeQQtQn\&q=85\&s=c420b05021d3fd9faf17e3f3a327c31a)

To move an event, select **Reschedule** and choose any upcoming window before the deadline, earlier or later than the one scheduled. You can reschedule each event up to 3 times. Only database administrators can reschedule maintenance.

![Reschedule maintenance dialog showing the current scheduled time, the must-run-by deadline, and a list of upcoming windows to choose from](https://mintcdn.com/planetscale-2/ChSZMNGaPLeQQtQn/postgres/cluster-configuration/maintenance-windows-reschedule.png?fit=max\&auto=format\&n=ChSZMNGaPLeQQtQn\&q=85\&s=ff2b22629c1fd9cafd7c0fb02071a858)

### Apply the update yourself

If you don't want to wait for your window, [apply the update](https://planetscale.com/docs/postgres/cluster-configuration/updates.md) from the **Clusters** page. When your window opens there's nothing left to change, so the maintenance completes without a restart.

### What happens during the restart

Each Postgres instance restarts once, replicas first. If your branch has replicas, we switch the primary over to an updated replica before restarting it, and writes pause briefly while the switchover completes. If your branch has no replicas, Postgres is unavailable while it restarts, usually for under a minute.

## Reduce the impact of a restart

The impact of a Postgres restart to your clients depends on how they are connected:

* **A dedicated primary PgBouncer.** Client connections stay open through planned switchovers, and queries wait briefly instead of failing. See [dedicated primary PgBouncers](https://planetscale.com/docs/postgres/connecting/pgbouncer.md#dedicated-primary-pgbouncers).
* **Reconnect and retry.** A restart closes connections made directly to Postgres or through the local PgBouncer. Make sure your application reconnects and [retries](https://planetscale.com/docs/postgres/connection-resilience.md#retries).

The [connection resilience guide](https://planetscale.com/docs/postgres/connection-resilience.md) covers each of these in detail, and [operations philosophy](https://planetscale.com/docs/postgres/operations-philosophy.md#image-upgrades) explains how image updates work.

## Webhooks

Subscribe to these [webhook events](https://planetscale.com/docs/api/webhook-events.md) to get the same notices programmatically:

* `maintenance.scheduled`
* `maintenance.started`
* `maintenance.completed`
* `maintenance.failed`
* `maintenance.canceled`

## API

* [List maintenance events](https://planetscale.com/docs/api/reference/list_maintenance_events.md)
* [Get a maintenance event](https://planetscale.com/docs/api/reference/get_maintenance_event.md)
* [Reschedule a maintenance event](https://planetscale.com/docs/api/reference/reschedule_maintenance_event.md)
* [Get a maintenance schedule](https://planetscale.com/docs/api/reference/get_maintenance_schedule.md)
* [Update a maintenance schedule](https://planetscale.com/docs/api/reference/update_maintenance_schedule.md)
* [List maintenance windows](https://planetscale.com/docs/api/reference/list_maintenance_windows.md)

## Development branches

Development branches don't have maintenance windows. Updates can restart them at any time.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
