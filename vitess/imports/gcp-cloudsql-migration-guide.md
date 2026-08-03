---
url: https://planetscale.com/docs/vitess/imports/gcp-cloudsql-migration-guide
title: "Gcp Cloudsql Migration Guide"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# GCP Cloud SQL Migration Guide

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

<PlatformAvailability current="vitess" />

## Overview

This document will demonstrate how to migrate a database from Google Cloud Platform (GCP) Cloud SQL MySQL Cluster to PlanetScale using our [Import tool](database-imports.md).

<Note>
  This guide assumes you are using MySQL on GCP. Other database systems available through GCP will not work with the
  PlanetScale import tool.
</Note>

We recommend reading through the [Database import documentation](database-imports.md) to learn how our import tool works before proceeding.

## Prerequisites

Before you can perform a migration, gather the following information from the GCP Console:

* **Public IP address** - Found in the **Overview** tab of your Cloud SQL cluster under the **Connect to this instance** section
* **Database name** - The name of the database you want to import
* **Root username and password** - You'll need these to create the migration user

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-ip-address.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=9cd3188f3de4861f6b3be0b094ccb35f" alt="The GCP Cloud SQL console with the IP address highlighted." width="1754" height="1330" data-path="images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-ip-address.png" />
</Frame>

A list of your databases can be found in the **Databases** tab. In this guide, we'll be using the `prod` database.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-databases.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=dc74d3b6e3bf087647a6f0010a0053e2" alt="The Databases list in the GCP console." width="1982" height="1038" data-path="images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-databases.png" />
</Frame>

## Create a migration user

Create a migration user account with limited privileges for the import process. **You must run this as the root user or another user with admin privileges.** Connect to your Cloud SQL instance as root using the MySQL command line:

```bash theme={null}
mysql -u root -p -h [your-cloud-sql-ip]
```

Then run the following script, making sure to update the placeholders:

* `<SUPER_STRONG_PASSWORD>` - The password for the `migration_user` account
* `<DATABASE_NAME>` - The name of the database you will import into PlanetScale

```sql theme={null}
CREATE USER 'migration_user'@'%' IDENTIFIED BY '<SUPER_STRONG_PASSWORD>';
GRANT PROCESS, REPLICATION SLAVE, REPLICATION CLIENT, RELOAD ON *.* TO 'migration_user'@'%';
GRANT SELECT, INSERT, UPDATE, DELETE, SHOW VIEW, LOCK TABLES ON `<DATABASE_NAME>`.* TO 'migration_user'@'%';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ALTER ON *.* TO 'migration_user'@'%';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ALTER ON `_vt`.* TO 'migration_user'@'%';
GRANT SELECT ON mysql.db TO 'migration_user'@'%';
GRANT SELECT ON mysql.func TO 'migration_user'@'%';
GRANT SELECT ON mysql.innodb_table_stats TO 'migration_user'@'%';
GRANT SELECT ON mysql.tables_priv TO 'migration_user'@'%';
GRANT SELECT ON mysql.user TO 'migration_user'@'%';
GRANT SELECT ON performance_schema.* TO 'migration_user'@'%';
FLUSH PRIVILEGES;
```

Verify the grants were applied correctly:

```sql theme={null}
SHOW GRANTS FOR 'migration_user'@'%';
```

You should see all the GRANT statements listed. If you only see one or two lines, the grants didn't apply correctly.

<Note>
  **Important**

  You must create the migration user on the MySQL command line and not in the GCP console. Creating users through the GCP console automatically grants the `cloudsqlsuperuser` role, which will cause the import to fail.
</Note>

## Allow PlanetScale to connect to your Cloud SQL instance

For PlanetScale to connect to your database, you'll need to update the Authorized networks for your cluster. The specific IP addresses to permit are shown during the import workflow on the **Connect to external database** step. The list includes IP addresses specific to your PlanetScale database region.

See the [Import public IP addresses](import-tool-migration-addresses.md) page for more details on where to find these IP addresses in the workflow. To permit traffic from these IP addresses to your database in GCP, select **Connections** from the navigation on the left. Under **Authorized networks**, click “**Add network**”. This will display an inline form for you to add a network. The name of the field is arbitrary, but the **Network** field should contain the IP address that needs access to your database. Click “**Done**” to add the new entry. Perform this step for each IP address for the selected region, then click “**Save**” to apply the settings.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-networking.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=0db785aa2f986ecab3ee5fefbc8b595b" alt="The form to add a new authorized network in the GCP console." width="1448" height="1556" data-path="images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-networking.png" />
</Frame>

## Configure MySQL server settings

Certain MySQL server settings may need to be changed before you can begin the import. The initial connection test will fail if these settings are not configured correctly.

* binlog\_expire\_logs\_seconds

To set a flag in your GCP console, go to your database's “**Overview**” page, select the “**Edit**” button, and then scroll down to the “**Flags**” section.

You want to select the “**binlog\_expire\_logs\_seconds**” flag and set it to `172800` seconds.

Make sure to select the “**Done**” button.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-set-flags.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=5cbff9170b7b587951aed8ead440b118" alt="The form to set MySQL flags." width="1490" height="1560" data-path="images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-set-flags.png" />
</Frame>

* log\_bin

If `log_bin` is set to OFF you may need to [enable Point in Time Recovery (PITR)](https://cloud.google.com/sql/docs/mysql/backup-recovery/pitr#enablingpitr) from the GCP console to start binary logging.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-enable-pitr.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=71f8ae6138e6f5c112415957de2a0216" alt="The form to set enable point in time recovery." width="2118" height="1644" data-path="images/assets/docs/imports/gcp-cloudsql-migration-guide/cloudsql-enable-pitr.png" />
</Frame>

## Importing your database

Now that your GCP Cloud SQL database is configured and ready, follow the [Database Imports guide](database-imports.md) to complete your import.

When filling out the connection form in the import workflow, use the following information:

* **Host name** - Your GCP Cloud SQL public IP address (from Prerequisites)
* **Port** - 3306 (default for Cloud SQL)
* **Database name** - The exact database name to import
* **Username** - `migration_user`
* **Password** - The password you set for the migration user
* **SSL verification mode** - Select based on your Cloud SQL SSL configuration

The Database Imports guide will walk you through:

* Creating your PlanetScale database
* Connecting to your Cloud SQL database
* Validating your configuration
* Selecting tables to import
* Monitoring the import progress
* Switching traffic and completing the import

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
