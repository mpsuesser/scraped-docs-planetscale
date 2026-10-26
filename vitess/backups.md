---
url: https://planetscale.com/docs/vitess/backups
title: "Backups"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Back up and restore

This page covers Vitess.

Postgres version: [Postgres](https://planetscale.com/docs/postgres/backups.md)

## Overview

PlanetScale provides the ability to create, schedule, and restore backups for production and development database branches.

> [!NOTE]
>
> Our [Base plan](https://planetscale.com/docs/planetscale-plans.md) includes automated backups every 12 hours.

## View backups

To view backups for all of your branches, go to your database backups page: `app.planetscale.com/<org>/<database>/backups`.

Once there, you'll find additional details about your backup history.

![View backups for your database](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/back-up-and-restore/view-backups.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=d69339fc9344ae5325dfbdfa9ba887ca)

## Create manual backups

In addition to the daily default backups that PlanetScale schedules for your database branches, you can create additional **manual** backups.

To create a manual backup, follow the steps outlined below:

**Step 1**

Go to your database backups page: `app.planetscale.com/<org>/<database>/backups`.

**Step 2**

Click the **Create new backup** button.

This will bring up a pop-up modal that prompts you to pick a **branch** to backup, *name* your backup, and select how long you wish to keep the backup.

![Manual backup pop-up modal](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/back-up-and-restore/create-new-backup.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=f077d4384de73a1057de5dc6736cd28b)

**Step 3**

Click the **Create backup** button to finish the backup and close the pop-up modal.

**Step 4**

Manual backups are not free and are denoted by the `$` icon next to their name in the list view on the backups page.

![Manual backups are shown with a \$ sign](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/back-up-and-restore/manual-backup-row.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=4d8b0a32c70a633c877977d5b6014ede)

**Step 5**

To see the cost associated with storage of a manual backup, click on the backup name. You will see the cost per month on the details page.

![Monthly storage cost for a Manual backup](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/back-up-and-restore/manual-backup-cost.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=4cffe60bd30ece07b0f67f72140bb8f9)

## Schedule backups

You can add additional **scheduled backups** for your branches, billed at \$0.023 per GB per month.

**Step 1**

Go to your database backups page: `app.planetscale.com/<org>/<database>/backups`.

**Step 2**

Select the type of branch (`Production branches` | `Development branches` ) you'd like to be backed up by the new backup schedule.

![Schedule backup pop-up modal](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/back-up-and-restore/new-backup-schedule.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=a969c26c114388ca3d745a8b0e4b8e20)

**Step 3**

Click the **Add new schedule** button.

This will bring up a pop-up modal that prompts you to configure backup frequency, select how long you wish to keep the backup, and to *name* your **schedule**.

![Schedule backup pop-up modal](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/back-up-and-restore/new-backup-schedule.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=a969c26c114388ca3d745a8b0e4b8e20)

**Step 4**

Click the **Save schedule** button to save your new scheduling configurations and to close the pop-up modal.

> [!NOTE]
>
> For additional scheduled backups beyond the included default (every 12 hours for the
> [Base plan](https://planetscale.com/docs/planetscale-plans.md), you will be billed
> **\$0.023 per GB per month**.

## Restore from a backup

To restore a backup to a new branch, click on the individual backup to see the option to restore them.

**Step 1**

Go to your database backups page: `app.planetscale.com/<org>/<database>/backups`.

**Step 2**

Select the backup you wish to restore.

**Step 3**

Click the **Restore backup** button.
This will bring up a pop-up modal that prompts you to name your branch and select a cluster size.

![Restore backup pop-up modal](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/backups/restore.png?fit=max\&auto=format\&n=AJPY38bILe2zenXX\&q=85\&s=4c122bc21296c2c62c30788494389819)

**Step 4**

Click the **Restore backup** button to finish restoring your backup and to close the pop-up modal.

**Step 5**

To see all branches that are `restored` from a backup, head to the backup details and you'll see a list of those branches.

![Branches restored from a backup](https://mintcdn.com/planetscale-2/pncBnOWnwAfGxG1f/images/assets/docs/concepts/back-up-and-restore/restored-branches-list.png?fit=max\&auto=format\&n=pncBnOWnwAfGxG1f\&q=85\&s=c61762f206947680ff0f03d43d85cd06)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
