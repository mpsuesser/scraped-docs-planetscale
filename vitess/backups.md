---
url: https://planetscale.com/docs/vitess/backups
title: "Backups"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

PlanetScale provides the ability to create, schedule, and restore backups for production and development database branches.

Our [Base plan](../planetscale-plans.md) includes automated backups every 12 hours.

## View backups

To view backups for all of your branches, go to your database backups page: `app.planetscale.com/<org>/<database>/backups`.

Once there, you’ll find additional details about your backup history.

![View backups for your database](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/back-up-and-restore/view-backups.png?w=2500&fit=max&auto=format&n=pncBnOWnwAfGxG1f&q=85&s=b04869a9528bafd452fac63b83064479)

View backups for your database

## Create manual backups

In addition to the daily default backups that PlanetScale schedules for your database branches, you can create additional **manual** backups.

To create a manual backup, follow the steps outlined below:

## Schedule backups

You can add additional **scheduled backups** for your branches, billed at $0.023 per GB per month.

For additional scheduled backups beyond the included default (every 12 hours for the [Base plan](../planetscale-plans.md), you will be billed **$0.023 per GB per month**.

## Restore from a backup

To restore a backup to a new branch, click on the individual backup to see the option to restore them.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
