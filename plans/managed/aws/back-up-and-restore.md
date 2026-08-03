---
url: https://planetscale.com/docs/plans/managed/aws/back-up-and-restore
title: "Back Up And Restore"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Back up and restore in AWS

> PlanetScale Managed backup and restore functions like the hosted PlanetScale product. For more info, see [how to create, schedule, and restore backups for your PlanetScale databases](../../../vitess/backups.md).

To learn more about the backup and restore access levels, see the [database level permissions documentation](../../../security/access-control.md#database-level-permissions).

By default, databases are automatically backed up once per day to an S3 bucket in the customer's AWS Organizations member account. This default can be adjusted when working with PlanetScale Support. Configuring and validating additional backup frequencies is the customer's responsibility.

During the initial provisioning process, PlanetScale applies an S3 configuration to ensure that backups are encrypted at rest on Amazon S3.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
