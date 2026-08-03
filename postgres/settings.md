---
url: https://planetscale.com/docs/postgres/settings
title: "Settings"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

To access the settings:

## General Settings

### Database Name

The database name field displays your current database name. **This name cannot be changed** because it’s used to connect to the database. Changing the name would break existing connections and applications.

### Default Branch

The default branch dropdown allows you to select which branch serves as the default for your database. This branch will be used as the source when creating new development branches.

Only production branches can be set as the default. New branches created from the **Branches** page start as development branches, so you must first [upsize the cluster and add replicas](cluster-configuration.md) on the **Clusters** page to promote them to a production branch before they are eligible.

A branch’s region cannot be changed after creation. To run production in a different region, create a new branch in the desired region, migrate your schema and data into it, and then set it as the default branch. See [Changing regions](branching.md#changing-regions) for the full procedure.

To change the default branch:

### Restrict Branch Regions

The “Restrict branch regions” option allows you to limit where new branches can be created geographically. When enabled, new branches can only be created in the same region as the default branch.

This setting controls where **new** branches can be created. It does not move existing branches, and it cannot change the region of the default branch or any other existing branch — branch regions are fixed at creation. See [Changing regions](branching.md#changing-regions) for more information.

To enable region restrictions:

This setting is useful for:

- Compliance requirements that mandate data residency
- Cost optimization by keeping resources in a specific region
- Reducing latency by keeping all branches in the same geographic area

### Save Settings

After making any changes to the general settings, click the **Save database settings** button to apply your changes.

## Delete Database

The settings page also provides the option to permanently delete your database. This action is irreversible and will:

- Delete the entire database and all of its branches
- Remove all data and backups permanently
- Disconnect any applications currently connected to the database
- Include usage charges in your final invoice through the deletion date

Database deletion cannot be undone. Make sure you have proper backups and that no critical applications depend on this database before proceeding.

To delete a database:

Only Organization Administrators and Database Administrators have permission to delete databases. See the [Access Control documentation](../security/access-control.md) for more information about user permissions.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
