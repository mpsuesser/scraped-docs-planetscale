---
url: https://planetscale.com/docs/vitess/integrations/hightouch
title: "Hightouch"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Hightouch integration

> Hightouch is a platform that allows you to sync data between systems using a simple and intuitive UI.

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

Hightouch supports PlanetScale databases as a data source and a destination to send data to. This article will provide an overview of setting up a PlanetScale database as a source and destination.

Connecting your PlanetScale database to a Hightouch workflow can be used in a number of different ways, but here are a few ideas to get you started:

* Automatically update customer records in your CRM when they are created or changed in your database.
* Send a notification to your Slack workspace when data is changed in your PlanetScale database.
* Execute asynchronous operations by sending data from your PlanetScale database to a data queue like AWS SQS.
* Automate paid ad targeting, suppression, and conversion uploads by syncing audiences from your database to ad platforms.
* Automatically sync account and user health scores to your customer success platforms.

Before you can proceed, you’ll need to create a set of credentials as covered in our [Connection strings doc](../connecting/connection-strings.md). Take note of the hostname, username, password, and database name.

## PlanetScale as a source

If you wish to sync data from your PlanetScale database to another system, you’ll need to configure your PlanetScale database as a source in Hightouch. I will be using a database named `bookings_db` in this demo.

### 1. Create the source

In Hightouch, start by selecting **"Sources"** from the sidebar.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/hightouch/sources.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=f68e372e3ce499be3486ef0804ff5031" alt="Sources" width="1999" height="1327" data-path="images/assets/docs/integrations/hightouch/sources.png" />
</Frame>

Click **"Add Source"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/add-source.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=0b8b300ce80853b70dfe57c81e5e1436" alt="Add source" width="1999" height="1327" data-path="images/assets/docs/integrations/hightouch/add-source.png" />
</Frame>

Select **"PlanetScale"** from the list of supported data sources.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/planetscale-as-a-data-source.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=db6df618980080dd3cdf43278dc262a1" alt="PlanetScale as a data source" width="1949" height="1285" data-path="images/assets/docs/integrations/hightouch/planetscale-as-a-data-source.png" />
</Frame>

On the Configure your PlanetScale source, populate Step 1 of the form using a set of credentials generated for your database. The **Port** will always be **3306**.

<Note>
  As a reminder, PlanetScale credentials can be generated by clicking **"Connect"** in the PlanetScale dashboard overview of your database. Review our [Connection strings doc](../connecting/connection-strings.md) for more details.
</Note>

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/connect-to-planetscale-data-source.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=43d35bf4caa90866e721c06ecada2fcf" alt="Connect to PlanetScale data source" width="1406" height="981" data-path="images/assets/docs/integrations/hightouch/connect-to-planetscale-data-source.png" />
</Frame>

Scroll down to Step 2, and populate your **User** and **Password** before clicking **"Continue"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/connect-to-planetscale-data-source-2.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=b8671e41cfa1a6a8a4461273ef17d41a" alt="Connect to PlanetScale data source 2" width="1406" height="1011" data-path="images/assets/docs/integrations/hightouch/connect-to-planetscale-data-source-2.png" />
</Frame>

In the next view, Hightouch will ensure that it can properly connect to your database. Provided it connects successfully, click **"Continue"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/hightouch/validate-connection.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=1d1e33552f2b4a8a98bcae3d8bbad386" alt="Validate connection" width="1750" height="1251" data-path="images/assets/docs/integrations/hightouch/validate-connection.png" />
</Frame>

Finally, name your source and click **"Finish"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/finalize-source-settings.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=2ac9835e9beac42bdbb418f4a5d8bc19" alt="Finalize source settings" width="1693" height="1232" data-path="images/assets/docs/integrations/hightouch/finalize-source-settings.png" />
</Frame>

### 2. Create a model

Before you can start syncing data, you need to tell Hightouch how the data looks with a Model. Start by selecting **"Models"** from the navigation.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/create-a-model.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=9fed2336170daa18157a7f7b5431385c" alt="Create a model" width="2156" height="1322" data-path="images/assets/docs/integrations/hightouch/create-a-model.png" />
</Frame>

Select the Source you created in the previous step.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/hightouch/planetscale-data-source-for-the-model.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=90f591d3560f484193fa3ed30b5d157a" alt="PlanetScale data source for the model" width="1580" height="1327" data-path="images/assets/docs/integrations/hightouch/planetscale-data-source-for-the-model.png" />
</Frame>

There are two available methods to model your data from a PlanetScale database:

* SQL query - allows you to write a SQL query that returns the data you want to sync.
* Table selector - allows you to select a table and read every row in it.

I’ll use **"Table selector"** for this demo.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/hightouch/query-vs-table-selector.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=ec4f1bd085ce46c5b4743107619623f2" alt="Query vs table selector" width="2279" height="1322" data-path="images/assets/docs/integrations/hightouch/query-vs-table-selector.png" />
</Frame>

Use the search to find the `hotels` table, select it from the list, and click **"Preview results"** to validate the data in that table. Once satisfied with the results, click **"Continue"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/new-model-from-hotels-table.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=4ac58e7b2a49508ccad5029269701864" alt="New model from hotels table" width="1830" height="1322" data-path="images/assets/docs/integrations/hightouch/new-model-from-hotels-table.png" />
</Frame>

Finally, name the model “Hotel” and set the Primary key to `id` before clicking **"Finish"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/configure-model.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=a4df28fecaaf7cd212b1ccebe6680b42" alt="Configure model" width="1830" height="1322" data-path="images/assets/docs/integrations/hightouch/configure-model.png" />
</Frame>

### 3. Create a sync

Now that the Source and Model are configured, a Sync can be created which will sync data from PlanetScale to another system. For this demo, I’ll be using a spreadsheet in my Google Drive account that I’ve already configured as a destination.

Select **"Syncs"** from the left and click **"Add sync"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/create-a-sync.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=13e32b82e974a12af2f71668ddc905d0" alt="Create a sync" width="2156" height="1322" data-path="images/assets/docs/integrations/hightouch/create-a-sync.png" />
</Frame>

Select the “Hotel” model created in the previous step.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/hightouch/select-the-hotel-model-for-the-sync.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=1ac749c64a5ce52461382d2b4d3df972" alt="Select the hotel model for the sync" width="2156" height="1322" data-path="images/assets/docs/integrations/hightouch/select-the-hotel-model-for-the-sync.png" />
</Frame>

For my demo, I’ll select my Google Sheets destination.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/hightouch/the-google-sheets-destination.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=bc730771162998ccacacd9e67ab54450" alt="The Google Sheets destination" width="1844" height="1322" data-path="images/assets/docs/integrations/hightouch/the-google-sheets-destination.png" />
</Frame>

I’m mirroring data into the default sheet in a Sheets doc named “Hightouch demo” for the Google Sheets settings.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/mirror-data-to-the-google-sheet.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=ad8f90e1024e32a845a4c2082b9daa73" alt="Mirror data to the Google Sheet" width="1550" height="1280" data-path="images/assets/docs/integrations/hightouch/mirror-data-to-the-google-sheet.png" />
</Frame>

A sync can be performed using a number of different methods. For the demo, I’ll leave **"Manual"** selected and click **"Finish"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/finalize-settings-for-first-sync.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=a71c4c9a5b24563f5adf331d16cce8fb" alt="Finalize settings for first sync" width="1550" height="1280" data-path="images/assets/docs/integrations/hightouch/finalize-settings-for-first-sync.png" />
</Frame>

Since this is a manual sync, I’ll also need to click **"Run sync"** to execute it.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/manually-run-first-sync.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=b6c05a3d483b972423471903bc63a0e0" alt="Manually run the first sync" width="1550" height="1280" data-path="images/assets/docs/integrations/hightouch/manually-run-first-sync.png" />
</Frame>

### 4. Review the synced data

If I log into my Google Drive account and review that spreadsheet, there are now three rows that exist which match the data from the `hotels` table of my PlanetScale database.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/hightouch/the-synced-data-in-google-sheets.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=0730c48413e264b97d1daf9045edeb00" alt="The synced data in Google Sheets" width="1550" height="1280" data-path="images/assets/docs/integrations/hightouch/the-synced-data-in-google-sheets.png" />
</Frame>

## PlanetScale as a destination

Hightouch can also be configured to send data to a PlanetScale database. In this demo, I’ll perform the inverse of the above demo and configure the `new hotels` sheet from my Google Sheets spreadsheet to add new rows to my PlanetScale database.

Here is that sheet with a single new hotel added to it.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/hightouch/the-new-hotels-sheet-in-google-sheets.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=6b67a82b979d7ea91d9fb54f2a4c4ad5" alt="The new hotels sheet in Google Sheets" width="1550" height="1280" data-path="images/assets/docs/integrations/hightouch/the-new-hotels-sheet-in-google-sheets.png" />
</Frame>

### 1. Create the Destination

Configuring the source and model for Google Sheets will not be covered in this demo. I’ll start by creating a new destination for my PlanetScale database by selecting **"Destinations"** from the left navigation, then **"Add destination"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/add-destination.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=24ab5291bb949a4ef5077aee13585719" alt="Add Destination" width="1550" height="1280" data-path="images/assets/docs/integrations/hightouch/add-destination.png" />
</Frame>

Search for the PlanetScale destination, select it, and click **"Continue"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/hightouch/select-planetscale-as-the-destination.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=edbc3c3f113a98a5ed7c5ea0dbffd201" alt="Select PlanetScale as the destination" width="1550" height="1280" data-path="images/assets/docs/integrations/hightouch/select-planetscale-as-the-destination.png" />
</Frame>

In the next view, populate the details in the form using the connection details for your PlanetScale database. Click **"Continue"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/connect-to-planetscale-as-a-destination.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=fb7b91f3cc2a9ed8aec76188ed02b12d" alt="Connect to PlanetScale as a destination" width="1596" height="1280" data-path="images/assets/docs/integrations/hightouch/connect-to-planetscale-as-a-destination.png" />
</Frame>

Click **"Finish"** to save the Destination.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/finalize-planetscale-destination.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=cdce985a615ba7fce4c336d6c2d4c7e5" alt="Finalize PlanetScale destination" width="1596" height="1280" data-path="images/assets/docs/integrations/hightouch/finalize-planetscale-destination.png" />
</Frame>

### 2. Create the Sync

To create a new Sync, select **"Syncs"** from the left navigation and click **"Add sync"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/add-second-sync.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=ed2e8a42d8197dc9622424dd42c9e91a" alt="Add second sync" width="2007" height="1280" data-path="images/assets/docs/integrations/hightouch/add-second-sync.png" />
</Frame>

The model name for my Google Sheets worksheet is `new_hotel` so I’ll select that from the list.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/hightouch/select-google-sheets-as-a-source.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=0be3694d8a9218e148dc6bba0492807d" alt="Select Google Sheets as a source" width="1806" height="1291" data-path="images/assets/docs/integrations/hightouch/select-google-sheets-as-a-source.png" />
</Frame>

Select the Destination created in the previous step.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/hightouch/select-the-planetscale-destination.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=ec314c8d7e7841bebc07a573059e7a71" alt="Select the PlanetScale destination" width="1806" height="1291" data-path="images/assets/docs/integrations/hightouch/select-the-planetscale-destination.png" />
</Frame>

Next, I can select the table I wish to sync data to. I’ll choose `hotels` from the dropdown. Hightouch only supports upserting data at this time, so that option is preselected. I’ll also need to map the fields between my Google Sheets worksheet and my PlanetScale database, starting with the unique identifiers.

Both have an `id` column so I can select that for both sides of the sync. Since the other column names match, I can click **"Suggest mappings"** to let Hightouch automatically figure out which fields map from the Source to the Destination. Once that’s done, I’ll click **"Continue"** to move forward.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/configure-the-field-mapping.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=a9330e7866039d6a949cd2f71feea479" alt="Configure the field mapping" width="1502" height="1378" data-path="images/assets/docs/integrations/hightouch/configure-the-field-mapping.png" />
</Frame>

Finally, I can specify the Schedule type for when my data sync should occur. I’ll leave it set to **"Manual"** and click **"Continue"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/hightouch/finalize-the-second-sync.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=01ef881807024fd08fa2dec71d649ef8" alt="Finalize the second sync" width="1502" height="1378" data-path="images/assets/docs/integrations/hightouch/finalize-the-second-sync.png" />
</Frame>

Since this Sync is configured to execute manually, I’ll click **"Run sync"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/hightouch/run-the-second-sync.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=7ddc3c970faf8ea494878a4d3d0e5a45" alt="Run the second sync" width="1502" height="1378" data-path="images/assets/docs/integrations/hightouch/run-the-second-sync.png" />
</Frame>

### 3. Review the synced data

I can verify that the sync was successful by querying my PlanetScale database to ensure the new hotel was added successfully.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/hightouch/the-select-query-from-the-planetscale-console.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=6aa14b36bf32a9dfb3891ccb2539eb98" alt="The select query from the PlanetScale console" width="1502" height="1378" data-path="images/assets/docs/integrations/hightouch/the-select-query-from-the-planetscale-console.png" />
</Frame>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
