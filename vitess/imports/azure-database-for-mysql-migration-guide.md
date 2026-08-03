---
url: https://planetscale.com/docs/vitess/imports/azure-database-for-mysql-migration-guide
title: "Azure Database For Mysql Migration Guide"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Azure Database for MySQL migration guide

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

This document will demonstrate how to migrate a database from Azure Database for MySQL to PlanetScale. We recommend reading through the [Database import documentation](database-imports.md) to learn how our import tool works before proceeding.

## Prerequisites

Before you can perform the migration, you’ll need to gather the following information from you MySQL instance in Azure:

* Server name
* Server admin login name
* Server admin password
* Database name

The server name and admin login name can be located on the **Overview** tab of the MySQL instance in Azure.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/azure-database-for-mysql-migration-guide/the-server-name-and-server-admin-login-name-located-in-the-azure-dashboard.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=a2a57e5a657f72caa1c47b35b50c0a88" alt="The server name and server admin login name located in the Azure dashboard." width="1718" height="498" data-path="images/assets/docs/imports/azure-database-for-mysql-migration-guide/the-server-name-and-server-admin-login-name-located-in-the-azure-dashboard.png" />
</Frame>

The server admin password is the same password you set when initially creating the database instance.

To view your available databases, select the **Databases** tab from the sidebar.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/azure-database-for-mysql-migration-guide/the-databases-tab-of-the-azure-dashboard.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=17320c7c4a9ce343dbac48c30f9e86ad" alt="The databases tab of the Azure dashboard." width="865" height="502" data-path="images/assets/docs/imports/azure-database-for-mysql-migration-guide/the-databases-tab-of-the-azure-dashboard.png" />
</Frame>

## Configure firewall rules

In order for PlanetScale to connect to your Azure database, you must allow traffic into the database through the associated security group. The specific IP addresses you will need to allow depend on the region you plan to host your PlanetScale database. Check the [Import tool public IP addresses page](import-tool-migration-addresses.md) to determine the IP addresses to allow before continuing. This guide will use the **AWS us-east-1 (North Virginia)** region so we’ll allow the following addresses:

```
3.209.149.66
3.215.97.46
34.193.111.15
23.23.187.137
52.6.141.108
52.70.2.89
50.17.188.76
52.2.251.189
52.72.234.74
35.174.68.24
52.5.253.172
54.156.81.4
34.200.24.255
35.174.79.154
44.199.177.24
```

To allow traffic into your Azure database, navigate to the “**Networking**” section from the sidebar and locate the **Firewall rules** section. There are already a series of inputs allowing you to add entries into the Firewall rules, each of which will permit network traffic from that IP address. Add a new entry for each address required, then click “Save” from the toolbar.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/azure-database-for-mysql-migration-guide/the-networking-tab-of-the-azure-dashboard.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=5ee562b7199fc5a58c188ea1bad757c8" alt="The networking tab of the Azure dashboard." width="1709" height="939" data-path="images/assets/docs/imports/azure-database-for-mysql-migration-guide/the-networking-tab-of-the-azure-dashboard.png" />
</Frame>

## Configure MySQL server settings

There are three settings that need to be configured before you can import your database:

* gtid\_mode
* enforce\_gtid\_consistency
* binlog\_row\_image

To access these settings in Azure, select “**Server parameters**” from the sidebar and enter “**gtid**” in the search bar. Set both “**enforce\_gtid\_consistency**” and “**gtid\_mode**” to “**ON**”. Next, search for “**binlog\_row\_image**” and set to “**full**”. Click “**Save**”.

<Note>
  For “**gtid\_mode**”, you’ll need to update the value in sequence displayed in the dropdown until it is set to “**ON**”. For example, if the current setting is “**OFF\_PERMISSIVE**”, you’ll need to first change it to “**ON\_PERMISSIVE**”, save the changes, then set it to “**ON**” in that order.
</Note>

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/azure-database-for-mysql-migration-guide/how-to-access-gtid-settings-in-the-azure-dashboard.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=8bb053b7f89564cf3b7b120811e4cf8f" alt="How to access gtid settings in the Azure dashboard." width="2132" height="1274" data-path="images/assets/docs/imports/azure-database-for-mysql-migration-guide/how-to-access-gtid-settings-in-the-azure-dashboard.png" />
</Frame>

## Import your database

Now that your Azure Database for MySQL is configured and ready, follow the [Database Imports guide](database-imports.md) to complete your import.

When filling out the connection form in the import workflow, use the following information:

* **Host name** - Your Azure server name (from Prerequisites)
* **Port** - 3306 (default for Azure MySQL)
* **Database name** - The exact database name to import
* **Username** - Your server admin login name
* **Password** - Your server admin password
* **SSL verification mode** - Select "**Verify Identity**" (Verify certificate and hostname)

The Database Imports guide will walk you through:

* Creating your PlanetScale database
* Connecting to your Azure MySQL database
* Validating your configuration
* Selecting tables to import
* Monitoring the import progress
* Switching traffic and completing the import

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
