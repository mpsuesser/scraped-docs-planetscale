---
url: https://planetscale.com/docs/vitess/integrations/airbyte
title: "Airbyte"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Airbyte integration

> With PlanetScale Connect, you can extract data from your PlanetScale database and safely load it into other destinations for analysis, transformation, and more.

This page covers Vitess.

We implemented an [Airbyte](https://airbyte.com/) connector as the pipeline between your PlanetScale source and selected destination. This document will walk you through how to connect your PlanetScale database to Airbyte.

## Connect to Airbyte

Only [Airbyte Open Source](https://docs.airbyte.com/quickstart/deploy-airbyte) supports the PlanetScale data source. In this section, you'll learn how to set up Airbyte and connect your PlanetScale source.

### Requirements

* A PlanetScale database
* [Docker Desktop](https://www.docker.com/products/docker-desktop/) (Docker terms apply)

### Set up Airbyte locally

**Step 1**

Install [Docker Desktop](https://www.docker.com/products/docker-desktop/).

**Step 2**

Follow the related installation instructions included within the [Airbyte Quickstart Documentation](https://docs.airbyte.com/using-airbyte/getting-started/oss-quickstart).

**Step 3**

Open Airbyte in the browser at [http://localhost:8000](http://localhost:8000).

### Set up PlanetScale source

Now that Airbyte is running locally, let's set up the custom PlanetScale source.

**Step 1**

In the Airbyte dashboard, click "**Settings**" on the bottom left.

**Step 2**

Click "**Sources**" on the left sidebar.

**Step 3**

Click the "**New connector**" button.

**Step 4**

Click the "**Add a new Docker connector**" option.
Fill in the connector values as follows:

* **Connector display name**: PlanetScale
* **Docker repository name**: planetscale/airbyte-source
* **Docker image tag**: `latest`
* **Connector Documentation URL**:(/vitess/integrations/airbyte

You can find the [PlanetScale Airbyte Source Dockerhub release page here](https://hub.docker.com/r/planetscale/airbyte-source).

![Airbyte new PlanetScale connector](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/airbyte/modal.png?fit=max\&auto=format\&n=89X51wIXzJwNfurq\&q=85\&s=9c89d059bcd4372b9a95336817af9a6f)

### Fill in PlanetScale connection information

You're now ready to connect your PlanetScale database to Airbyte.

**Step 1**

Click on the database and branch you want to connect to.

**Step 2**

Click "**Connect**", select "**General**" from the "**Connect with**" dropdown.

**Step 3**

Leave this tab open, as you'll need to copy these credentials shortly.

**Step 4**

Back in Airbyte, click "**Sources**" in the main left sidebar > "**New source**".

**Step 5**

Select the new PlanetScale source you created from the dropdown.

**Step 6**

Fill in the "**Set up the source**" values as follows:

* **Name**: Any name of your choice
* **Source type**: Select "PlanetScale"
* **Host**: Paste in the copied value for `host`
* **Database**: Paste in the copied value for `database`
* **Username**: Paste in the copied value for `username`
* **Password**: Paste in the copied value for `password`

![Airbyte - PlanetScale source setup](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/airbyte/db-info.png?fit=max\&auto=format\&n=89X51wIXzJwNfurq\&q=85\&s=53101c9baf86f5b9ceabe6db28caa8e4)

**Step 7**

You can also provide some optional values:

* **Replicas**: Select whether or not you want to collect data from replica nodes.
* **Shards**: Map your shards.
* **Starting GTIDs**: Start replication from a specific GTID per keyspace shard.

![Airbyte - PlanetScale optional setup](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/airbyte/optional.png?fit=max\&auto=format\&n=89X51wIXzJwNfurq\&q=85\&s=fe5f352db1557ac739cc0939060529ae)

You can see the [PlanetScale airbyte-source README](https://github.com/planetscale/airbyte-source/blob/main/README.md) for more details on these options.

**Step 8**

Click "**Set up source**" to connect.

You should get a success message that the connection test passed.

### Choose your destination

With the connection complete, you can now choose your destination.

**Step 1**

Click "**Destinations**" in the sidebar or the "**New destination**" button on the source connection page.

**Step 2**

Set up the destination you want to sync your data to.

Each destination should have a Setup Guide linked on its destination setup page.

### Configure a connection

Now to get the connection fully set up.
Click on "Connections" on the left side bar.
If you have not yet set up any connectors, you should see this:

![Airbyte - New connection](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/airbyte/create.png?fit=max\&auto=format\&n=89X51wIXzJwNfurq\&q=85\&s=1983287a414b7bb7f90844ffddbc38ab)

Click the button to set up a connection.
Otherwise, click "**New Connection**" in the top right corner.
From here, follow these steps:

**Step 1**

On the "**Define source**" page, choose your PlanetScale source as the **source**.

![Airbyte - Source](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/airbyte/source.png?fit=max\&auto=format\&n=89X51wIXzJwNfurq\&q=85\&s=7ad44bdfc5e175db692cbf8d8fbfc4b5)

**Step 2**

On the "**Define destination**" page, select the **destination** you want to sync your PlanetScale data to.
For this demo, we are using a CSV destination.

![Airbyte - Source](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/airbyte/destination.png?fit=max\&auto=format\&n=89X51wIXzJwNfurq\&q=85\&s=2db7f41a6ef6f7e006d91fbb1691587d)

**Step 3**

On the "**Select streams**" page, select a sync mode.

![Airbyte - Source](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/airbyte/streams.png?fit=max\&auto=format\&n=89X51wIXzJwNfurq\&q=85\&s=2c8f5bcc55fc8ddbe190835ff5fb7a94)

**Step 4**

Also on this page, you will need to select the specific tables and columns you want to sync. For each, choose what type of sync mode you'd like to use for each source table.

![Airbyte - Sync](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/airbyte/sync.png?fit=max\&auto=format\&n=89X51wIXzJwNfurq\&q=85\&s=ba24aaa02dcc0ee19eeefd58dfa03420)

* **Incremental** — Incremental sync pulls *only* the data that has been modified/added since the last sync. We use [Vitess VStream](https://vitess.io/docs/concepts/vstream/) to track the stopping point of the previous sync and only pull any changes since then.
* **Full refresh** — Full refresh pulls *all* data at every scheduled sync frequency.

**Step 5**

On the "**Configure connection**" page, choose a sync frequency, which is how often we will connect to your PlanetScale database to download data.

![Airbyte - Connection](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/airbyte/connection.png?fit=max\&auto=format\&n=89X51wIXzJwNfurq\&q=85\&s=8eca240239c85b56d2a9388ec0b6a9e9)

**Step 6**

Click "**Finish and sync**".

Everything is now configured to pull your PlanetScale data into Airbyte and sync it to the selected destination on the schedule you chose. To run the connection, click "**Connections**" > "**Launch**".

## Handling schema changes

Airbyte will not automatically detect when you make schema changes to your PlanetScale database. If you drop a column, your sync should throw an error as it looks for a column that doesn't exist. However, if you add a column, the sync will continue without any errors. Airbyte will be unaware of the new column altogether. This is known as schema drift.

Whenever you perform a schema change, you need to notify Airbyte of it:

**Step 1**

In the Airbyte dashboard, click "**Connections**", select the connection, then navigate to the "**Schema**" tab.

**Step 2**

Click "**Refresh source schema**".

**Step 3**

Click "**Save changes**". Keep in mind, this might delete all data for the connection and start a new sync from scratch.

## Stopping Airbyte

At any point, you can disable any incremental or full syncs by going to the 'Connection' settings page and clicking 'Delete this connection'. This will not touch any of the source or destination data, but will prevent Airbyte from doing any further operations.

![Airbyte - PlanetScale disconnection](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/airbyte/delete.png?fit=max\&auto=format\&n=89X51wIXzJwNfurq\&q=85\&s=ddfa9b8ec7f6025cf29bba29b4d076d0)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
