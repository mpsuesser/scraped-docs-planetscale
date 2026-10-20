---
url: https://planetscale.com/docs/neki/cluster-configuration/maintenance-windows
title: "Maintenance Windows"
description: ""
access_date: 2026-10-20T18:26:07.257Z
current_date: 2026-10-20T18:26:07.257Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Configure Neki maintenance windows

> Weekly maintenance scheduling for production Neki branches.

Every production Neki branch has a weekly maintenance window. PlanetScale uses
this window for maintenance that can restart cluster components, including:

* Neki image updates.
* Postgres minor-version updates within the configured major version.
* Updates to default parameters and platform-managed configuration.

On a highly available shard, Neki performs a planned switchover before maintenance on the
primary. The switchover waits for a replica to catch up before promoting it,
which preserves commits made on the old primary. Maintenance can still close
active connections, so applications should reconnect automatically.

> [!NOTE]
>
> The maintenance window controls scheduled weekly maintenance. Configuration
> changes that you request in the dashboard, API, CLI, or Terraform apply
> independently of this window.

## Change the weekly schedule

Maintenance windows use UTC. The dashboard also shows the corresponding time
in your configured local time zone.

![Settings Maintenance page showing the weekly schedule card for a production branch, with Change schedule and Run maintenance tasks now](https://mintcdn.com/planetscale-2/6i1lJ6cNfB-VSsaH/neki/cluster-configuration/maintenance-windows.png?fit=max\&auto=format\&n=6i1lJ6cNfB-VSsaH\&q=85\&s=265c268fbc5ad3f5310cae26457d79da)

**Step 1**

From the PlanetScale dashboard, select your Neki database.

**Step 2**

Open **Settings**, then select **Maintenance**.

**Step 3**

Find the production branch and select **Change schedule**.

**Step 4**

Choose the day of the week and hour in UTC.

**Step 5**

Select **Save**.

The schedule card shows the previous maintenance window and the next scheduled
window. Only database administrators can change maintenance schedules.

## Run maintenance immediately

To apply available weekly maintenance without waiting for the next window,
select **Run maintenance tasks now** on the branch's schedule card.

Running maintenance immediately applies the same available updates as the
weekly schedule and can restart cluster components. Confirm that applications
can reconnect before starting it. Neki cannot start customer-requested
maintenance while a branch is sleeping.

Run branch-wide maintenance from the CLI with:

```bash theme={null}
pscale branch maintenance run <DATABASE> <BRANCH>
```

## Development branches

Development branches do not have configurable maintenance windows. Scheduled
maintenance uses PlanetScale's maintenance schedule for these branches.

## Related documentation

* [Cluster configuration](../cluster-configuration.md)
* [Postgres versions](../cluster-configuration.md#postgres-versions)
* [Replication and planned switchovers](../overview.md#the-admin)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
