---
url: https://planetscale.com/docs/vitess/imports/import-tool-migration-addresses
title: "Import Tool Migration Addresses"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Import public IP addresses

> When you [import a database](database-imports.md), you need to grant a set of IP addresses access to your external MySQL database so that PlanetScale can connect to it through an external keyspace.

## Overview

To import your external database into PlanetScale, you need to allowlist PlanetScale's IP addresses in your database firewall or security group. This lets PlanetScale connect to your database when you create the external keyspace and for the rest of the import.

## Where to find your IP addresses

The IP addresses depend on the region of your PlanetScale database.

**From the CLI:** list your organization's regions in JSON. Each region's `public_ip_addresses` field lists the IP addresses to allowlist:

```bash theme={null}
pscale region list --org <ORGANIZATION_NAME> --format json
```

Use the addresses for the region with the same `slug` as your database's region.

**From the dashboard:** when you [create an external keyspace](https://planetscale.com/docs/vitess/cluster-configuration.md#create-an-external-keyspace) from the **Clusters** page, the form lists the IP addresses that need access to your external database.

![IP addresses listed on the external database connection form](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/imports/import-workflows-ip-addresses/import-ips.png?fit=max\&auto=format\&n=89X51wIXzJwNfurq\&q=85\&s=fa15ffe65fbc1bd53493531dc5ec19ec)

**From the API:** the [list regions for an organization](https://planetscale.com/docs/api/reference/list_regions_for_organization.md) endpoint returns the same `public_ip_addresses` field.

## Provider-specific firewall guides

If you're connecting to a known database provider, see the provider's migration guide for how to allow these IP addresses:

* [Amazon Aurora](https://planetscale.com/docs/vitess/imports/amazon-aurora-migration-guide.md#step-6-configure-rds-security-group)
* [AWS RDS for MySQL](https://planetscale.com/docs/vitess/imports/aws-rds-migration-guide.md#step-6-configure-rds-security-group)
* [Azure Database for MySQL](https://planetscale.com/docs/vitess/imports/azure-database-for-mysql-migration-guide.md#configure-firewall-rules)
* [DigitalOcean](https://planetscale.com/docs/vitess/imports/digitalocean-database-migration-guide.md#update-trusted-sources)
* [Google Cloud SQL](https://planetscale.com/docs/vitess/imports/gcp-cloudsql-migration-guide.md#allow-planetscale-to-connect-to-your-cloud-sql-instance)

## Important notes

* **Region-specific IPs** - The IP addresses differ by region. Make sure you're using the IPs for your database's region.
* **IPs may change** - IP addresses can change occasionally. Check the current list before you start an import.
* **All IPs required** - You need to allowlist all the IP addresses listed, not just one.

> [!NOTE]
>
> This guide is meant to be used alongside the [Database imports guide](https://planetscale.com/docs/vitess/imports/database-imports.md) or one of the provider-specific migration guides.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
