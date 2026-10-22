---
url: https://planetscale.com/docs/postgres/backups/point-in-time-recovery
title: "Point In Time Recovery"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Point-in-time recovery

> Point-in-time recovery (PITR) allows you to restore your PlanetScale Postgres database to any specific moment within your backup retention window. Unlike traditional backups that restore to specific backup snapshots, PITR gives you precise control over when to restore your data.

## How PITR Works

PlanetScale Postgres uses [PostgreSQL's Write-Ahead Logging (WAL)](https://www.postgresql.org/docs/current/wal-intro.html) system combined with periodic backups to enable PITR.

PostgreSQL's WAL system records all changes to the database before they're applied to the actual data files. Each transaction is first written to the WAL log, ensuring durability even if the system crashes. These WAL files are continuously archived, creating a complete record of every change made to your database. During PITR, PostgreSQL starts with a base backup and then "replays" the relevant WAL files to reconstruct the exact database state at your specified point in time.

In short, there are 3 key things to know about:

* **Backups**: Snapshots of your database taken at regular intervals
* **WAL Archiving**: Continuous archiving of all database changes as they occur
* **Recovery Process**: Restores from a base backup and replays WAL files to reach the exact point in time requested

## Performing a point-in-time recovery

PITR is available for any point in time within your retention window — by default from 2 days ago up to 5 minutes before the current time (the 5-minute buffer ensures WAL files are fully processed and archived).

If you have created manual on-demand backups, have a custom backup schedule, or have enabled the **Prevent backup deletion** toggle on any backup, then PITR is available from the oldest backup available up to 5 minutes before the current time.

> [!NOTE]
>
> Additional backups beyond the default incur additional charges. See [Backups](https://planetscale.com/docs/postgres/pricing.md#backups) for more information.

### Create the PITR backup branch

**Step 1**

From the PlanetScale organization dashboard, select the desired database

**Step 2**

Navigate to the **Backups** page from the menu on the left

**Step 3**

On the right side of the page, you'll see a box labeled "**Point-in-time recovery**"

**Step 4**

Click the Branch dropdown to select the branch you'd like to restore from

**Step 5**

Select your target date and time within the available window

**Step 6**

Click **Restore backup**

**Step 1**

You'll see a summary of the selected branch, time you are restoring to, and size

**Step 2**

Name your new branch

**Step 3**

Select a cluster size

**Step 4**

Click "Restore backup"

This will create a new branch with the schema and data from the selected point in time. User-defined roles are restored the same way as a [backup restore](https://planetscale.com/docs/postgres/backups.md#roles-on-a-restored-branch): they keep object ownership, and their passwords are reset. See [Branching](https://planetscale.com/docs/postgres/branching.md) for next steps on how to connect and potentially promote your new branch.

## Restore time considerations

Restore time depends on three key factors:

* **Cluster size**: Data can only be restored as fast as your cluster can handle. Consider up-scaling the disk configuration (for network-attached storage) or upgrading your cluster size (for PlanetScale Metal)
* **Data volume**: The amount of data being restored affects initial backup restoration time
* **WAL replay duration**: The time between your chosen restore point and the nearest available backup affects WAL replay time

PostgreSQL must start with the closest available backup taken *before* your target restore point and replay all WAL files from that backup until the target time.

### Example 1: Restore to a point 8 hours ago

Your database uses the default backup schedule: backups every 12 hours, retained for 2 days. You want to restore to a point 8 hours ago.

```text theme={null}
Available Backups: [-------- 2 days retention --------]
                   |-------|-------|-------|-------|
                   48hrs   36hrs   24hrs   12hrs   NOW
                   (exp)   ✓       ✓       ✓
Restore Process:   |===============|~~~~~~~|
                   Base Backup     WAL Replay
                   (12hrs ago)     (4 hours)
                                   ↑
                                Target: 8hrs ago
Result: Quick restore (typically minutes to under an hour)
```

### Example 2: Restore to a point 16 days ago (with a custom backup schedule)

In addition to the default backup schedule, your database has a custom schedule: weekly backups every Sunday, retained for 31 days. You want to restore to a point 16 days ago (mid-week).

```text theme={null}
Available Backups: [-------------- 31 days retention --------------]
                   |----------|----------|----------|----------|
                   28d (Sun)  21d (Sun)  14d (Sun)  7d (Sun)   NOW
                   ✓          ✓          ✓          ✓
Restore Process:   |===============|~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~|
                   Base Backup     WAL Replay
                   (21 days ago)   (5 days)
                                   ↑
                                Target: 16 days ago
Result: Longer restore time (hours, depending on database activity volume)
```

The total restore time increases with the gap between your target restore point and the nearest prior backup. Databases with high transaction volume will have more WAL files to replay, extending the process.

## When to Use PITR

Point-in-time recovery is ideal for:

* **Data corruption recovery**: Restore to just before corruption occurred
* **Accidental deletions**: Recover data that was mistakenly removed
* **Failed migrations**: Roll back to before a problematic schema change
* **Compliance requirements**: Access historical data states for auditing

## Troubleshooting

### "Recovery point not available"

* The requested time may be outside your retention window
* Check that the timestamp is at least 5 minutes ago

### "WAL files missing"

* WAL archiving may have been interrupted
* Network connectivity issues during archival
* Storage system failures
* Consider using the nearest available backup point instead

If you need additional assistance, [contact](https://planetscale.com/contact?initial=support) the PlanetScale support team.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
