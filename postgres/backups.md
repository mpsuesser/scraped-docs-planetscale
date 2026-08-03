---
url: https://planetscale.com/docs/postgres/backups
title: "Backups"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Automatic scheduled backups

### Default backup schedule

All PlanetScale Postgres databases include automatic scheduled backups that run every 12 hours for production and development branches. Default backups are created automatically and don’t require any setup. There are no additional charges for these backups. WAL logs are retained for the same period as your oldest backup.

Both backups and WAL files are stored in highly durable, available, and secure storage off of your cluster, in the same cloud provider and region as the cluster.

## Additional backups

Custom backup schedules and manually created on-demand backups can incur additional charges. See [Backup Pricing](#backup-pricing) for more information.

### Custom backup schedules

You can create additional custom backup schedules for more frequent backups or specific timing requirements. You must be an organization administrator or database administrator to create or modify a custom backup schedule.

### Manual backups

You can create a one-off on-demand backup of your database branch:

### Emergency backups

Emergency backups are available when you need an immediate backup regardless of database state or ongoing operations. Unlike regular backups, emergency backups do not rely on previous backups or previous WALs.

They also may use additional primary database resources during creation. They are only recommended when regular backups are failing due to WAL corruption or other issues.

Emergency backups may impact database performance and should only be used in critical situations where immediate backup is required.

## Viewing backups

### Backup List

All backups can be viewed on your Backups page.

You can click into any backup to see the branch, expiration date, restore to a new branch, and to toggle preventing backup deletion.

## Restoring from Backups

### Creating a Restored Branch

Restore any backup to a new database branch:

A new branch will be created based on this backup and will become visible under the **Branches** page.

### Point-in-time recovery

PlanetScale Postgres supports precise [point-in-time recovery (PITR)](backups/point-in-time-recovery.md) using Write-Ahead Log (WAL) files. This allows you to restore to any specific moment within your backup retention window, not just to backup snapshots.

**Key benefits:**

- Restore to any timestamp between your oldest backup and 5 minutes prior to the current time
- More precise than traditional backup-only restoration
- Essential for recovering from data corruption or accidental changes

**Restore time considerations:** The time required depends on your cluster size, data volume, and how far back you’re restoring. Restoring to recent points (within default 12-hour backup intervals) is typically quick, while restoring to older points may require replaying multiple days of WAL files.

For detailed examples and step-by-step instructions, see the [Point-in-time recovery documentation](backups/point-in-time-recovery.md).

### Restored Branch Behavior

- Restored branches are independent database branches
- They include all data as of the restore point
- Restored branches are always created in the **same cloud region** as the source branch
- Database extensions are **not** restored - you’ll need to reinstall any extensions
- Restored branches can be promoted to production or used for testing
- Backup expiration is automatically extended to ensure the restored branch can create new backups

## Backup retention

The default, included backup schedule retains backups for 2 days. You can set a custom retention period for custom backup schedules and manual one-off backups.

Additionally, you can choose to protect any individual backup from deletion. Click on the backup you’d like to protect, and click the “ **Prevent backup deletion** ” toggle. Keep in mind that custom backups and included backups that are retained beyond the default retention policy will incur additional charges.

## Backup pricing

PlanetScale Postgres includes backup storage with every database branch. Each branch receives backup storage equal to **twice the allocated disk size**. For example, a database with 50 GB of storage includes 100 GB of backup storage.

To learn more about how backup storage is measured and pricing for additional storage, see [pricing](pricing.md).

### Monitor your usage

Track your backup storage usage and costs on the **Backups** page in your database dashboard. The page shows a breakdown of storage usage and any overage charges per branch, helping you optimize retention policies if needed.

If you need additional assistance, [contact](https://planetscale.com/contact?initial=support) the PlanetScale support team.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
