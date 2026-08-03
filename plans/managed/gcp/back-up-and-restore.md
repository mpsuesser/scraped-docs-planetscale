---
url: https://planetscale.com/docs/plans/managed/gcp/back-up-and-restore
title: "Back Up And Restore"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Back up and restore in GCP

> PlanetScale Managed backup and restore functions like the hosted PlanetScale product. For more info, see [how to create, schedule, and restore backups for your PlanetScale databases](../../../vitess/backups.md).

To learn more about the backup and restore access levels, see the [database level permissions documentation](../../../security/access-control.md#database-level-permissions).

By default, databases are automatically backed up once per day to a Cloud Storage bucket in the customer's GCP project. This default can be adjusted when working with PlanetScale Support. However, configuring and validating additional backup frequencies is the customer's responsibility.

During the initial provisioning process, PlanetScale applies a Cloud Storage configuration to ensure backups are encrypted at rest on GCP Cloud Storage.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
