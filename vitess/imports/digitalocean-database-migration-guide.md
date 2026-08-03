---
url: https://planetscale.com/docs/vitess/imports/digitalocean-database-migration-guide
title: "Digitalocean Database Migration Guide"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# DigitalOcean database migration guide

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

## Introduction

In this article, we’ll walk through migrating a MySQL database from DigitalOcean to PlanetScale using the [Import tool](database-imports.md).

<Note>
  This guide assumes you are using MySQL on DigitalOcean. Other database systems available through DigitalOcean will not work with the PlanetScale import tool.
</Note>

We recommend reading through the [Database import documentation](database-imports.md) to learn how our import tool works before proceeding.

## Prerequisites

Before you can start migrating your database, you’ll also need to collect the following information from your DigitalOcean cluster:

* The admin username and password
* The database host
* The port
* Database name

Most of this information is located on the landing page of your cluster.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/digitalocean-database-migration-guide/the-overview-of-the-database-cluster.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=1f29311908698e9fe2b2ee1876c1556b" alt="The Overview of the database cluster." width="1342" height="1059" data-path="images/assets/docs/imports/digitalocean-database-migration-guide/the-overview-of-the-database-cluster.png" />
</Frame>

You can view the list of databases in the "**Users & Databases**" tab. This article will use the default database created when the cluster was initialized, named `defaultdb`.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/digitalocean-database-migration-guide/the-users-and-databases-view-of-the-cluster-with-the-databases-section-highlighted.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=d8e3ced595e14b3f8089cdb9240a27e6" alt="The Users & Databases view of the cluster with the Databases section highlighted." width="1255" height="939" data-path="images/assets/docs/imports/digitalocean-database-migration-guide/the-users-and-databases-view-of-the-cluster-with-the-databases-section-highlighted.png" />
</Frame>

<Note>
  If you don’t know the admin password, you can create a new set of credentials using the information on the [Import
  tool user permissions page](import-tool-user-requirements.md) to create an account that can be used to
  import your database.
</Note>

## Update trusted sources

In order for PlanetScale to connect to your DigitalOcean database, you must allow network traffic into the database by adding the necessary IP addresses to the trusted sources list in DigitalOcean. The specific IP addresses you will need to allow depend on the region you plan to host your PlanetScale database. Check the [Import tool public IP addresses page](import-tool-migration-addresses.md) to determine the IP addresses to allow before continuing. This guide will use the **AWS us-east-1 (North Virginia)** region so we’ll allow the following addresses:

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

In the DigitalOcean dashboard, navigate to the “**Settings”** tab of your database and locate **Trusted sources** in the list of configuration items. Click “**Edit”** and the row should change to allow edits to the setting.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/digitalocean-database-migration-guide/the-settings-tab-of-the-database-cluster-in-digital-ocean.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=ef3106dc50ca4745df5f37c83b53d28c" alt="The Settings tab of the database cluster in Digital Ocean." width="1238" height="1138" data-path="images/assets/docs/imports/digitalocean-database-migration-guide/the-settings-tab-of-the-database-cluster-in-digital-ocean.png" />
</Frame>

When you enter an IP address from the list, a message will appear below the input box asking if you want to add that IP as an address. Click that message to add it to the list. Repeat this step for each IP address that needs to be added, then click “**Save**” once you are done.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/digitalocean-database-migration-guide/the-trusted-sources-section-of-the-settings-tab.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=a553916f587d5a1264a7e62b17a66b3f" alt="The Trusted sources section of the Settings tab." width="959" height="217" data-path="images/assets/docs/imports/digitalocean-database-migration-guide/the-trusted-sources-section-of-the-settings-tab.png" />
</Frame>

### Disable ANSI\_QUOTES setting

The default settings for MySQL databases on DigitalOcean is to have `ANSI_QUOTES` enabled in the global MySQL settings, which is not supported by PlanetScale. To remove this setting, navigate to the "**Settings**" tab of your cluster and locate the section titled **Global SQL mode**. Click “**Edit**” in that section to change the configuration settings.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/digitalocean-database-migration-guide/the-settings-tab-of-the-database-cluster-in-digitalocean-with-the-global-sql-mode-section-highlighted.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=33f2966ff29f5f1e09dc640ee5d6eef5" alt="The Settings tab of the database cluster in DigitalOcean with the Global SQL mode section highlighted." width="1284" height="1123" data-path="images/assets/docs/imports/digitalocean-database-migration-guide/the-settings-tab-of-the-database-cluster-in-digitalocean-with-the-global-sql-mode-section-highlighted.png" />
</Frame>

To remove the `ANSI_QUOTES` setting, click the “**x**” next to the tag and click “**Save**.” The change should apply immediately.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/TneybaJ6MA8SGyM3/images/assets/docs/imports/digitalocean-database-migration-guide/an-example-of-removing-the-ansi_quotes-setting-from-the-global-sql-mode-settings.png?fit=max&auto=format&n=TneybaJ6MA8SGyM3&q=85&s=14ca6da3f6473279e13ab4fe1be4c26e" alt="An example of removing the ANSI_QUOTES setting from the Global SQL mode settings." width="1197" height="517" data-path="images/assets/docs/imports/digitalocean-database-migration-guide/an-example-of-removing-the-ansi_quotes-setting-from-the-global-sql-mode-settings.png" />
</Frame>

### Set the Binlog Retention Period

The binary log related [MySQL server variables](database-imports.md#server-configuration-check) required for PlanetScale's importer are already set to acceptable values by default on Digital Ocean managed MySQL servers but there's one more variable to check on the "**Settings**" tab of your existing cluster.

Scroll down to "**Advanced configurations**" and ensure the "**Binlog Retention Period**" is set to the maximum value of `86400` seconds.

<img src="https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/imports/digitalocean-binlog-retention.png?fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=cc591d82d25ad5a9a5e81a35f6d47f60" alt="Setting the binlog retention period under Advanced Configurations" width="1998" height="582" data-path="vitess/imports/digitalocean-binlog-retention.png" />

## Importing your database

Now that your DigitalOcean database is configured and ready, follow the [Database Imports guide](database-imports.md) to complete your import.

When filling out the connection form in the import workflow, use the following information:

* **Host name** - Your DigitalOcean database host (from Prerequisites)
* **Port** - Your port (typically 25060 for DigitalOcean)
* **Database name** - The exact database name to import (e.g., `defaultdb`)
* **Username** - Your admin username
* **Password** - Your admin password
* **SSL verification mode** - Select based on your DigitalOcean SSL configuration

The Database Imports guide will walk you through:

* Creating your PlanetScale database
* Connecting to your DigitalOcean database
* Validating your configuration
* Selecting tables to import
* Monitoring the import progress
* Switching traffic and completing the import

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
