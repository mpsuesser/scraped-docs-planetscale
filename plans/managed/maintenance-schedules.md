---
url: https://planetscale.com/docs/plans/managed/maintenance-schedules
title: "Maintenance Schedules"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Maintenance schedules

> Enterprise customers have the option to enable scheduled maintenance windows on databases.

**Platform availability:** Vitess and Postgres

If you have a maintenance schedule enabled, any changes to your cluster configuration will only roll out during the set maintenance period. This includes database engine version upgrades (MySQL/Vitess or Postgres), increasing or decreasing cluster size, changes to the size of cluster components, and anything else that modifies your cluster configuration.

The only exceptions to this are the following:

* For MySQL databases: changes to the *number* of VTGates

Modifications to your schema, such as [deploy requests](../../vitess/schema-changes/deploy-requests.md) and [workflows](../../vitess/scaling/workflows.md), are not included in the maintenance schedule and will complete as soon as you apply the changes.

## Enabling maintenance schedules

To enable the use of maintenance schedules, you must first reach out to our [Support team](https://planetscale.com/contact).

Once the feature is enabled on a database, you can configure the maintenance windows for each of your databases by clicking the database > Settings > Maintenance.

Maintenance schedules can be set up on a daily, weekly, or monthly basis at a specific time in UTC. When occurring weekly, you can set the day of the week. When occurring monthly, you can set the day of the week and which week of the month over which the maintenance will occur.

To modify a maintenance schedule, click on the available schedule, and then adjust via the dropdowns.

If you need to make a change during an emergency, you can disable the maintenance schedule. That will result in the branch immediately running any queued tasks. If you are making a change in an emergency, we recommend that you perform the sizing operation first to queue the change, and then disable the maintenance schedule.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
