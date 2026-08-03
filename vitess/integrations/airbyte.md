---
url: https://planetscale.com/docs/vitess/integrations/airbyte
title: "Airbyte"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

We implemented an [Airbyte](https://airbyte.com/) connector as the pipeline between your PlanetScale source and selected destination. This document will walk you through how to connect your PlanetScale database to Airbyte.

## Connect to Airbyte

Only [Airbyte Open Source](https://docs.airbyte.com/quickstart/deploy-airbyte) supports the PlanetScale data source. In this section, you’ll learn how to set up Airbyte and connect your PlanetScale source.

### Requirements

- A PlanetScale database
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (Docker terms apply)

### Set up Airbyte locally

### Set up PlanetScale source

Now that Airbyte is running locally, let’s set up the custom PlanetScale source.

You can find the [PlanetScale Airbyte Source Dockerhub release page here](https://hub.docker.com/r/planetscale/airbyte-source).

![Airbyte new PlanetScale connector](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/airbyte/modal.png?w=2500&fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=6b62cacc1c40dd661a1309c8257d5db2)

Airbyte new PlanetScale connector

### Fill in PlanetScale connection information

You’re now ready to connect your PlanetScale database to Airbyte.

You should get a success message that the connection test passed.

### Choose your destination

With the connection complete, you can now choose your destination.

Each destination should have a Setup Guide linked on its destination setup page.

### Configure a connection

Now to get the connection fully set up. Click on “Connections” on the left side bar. If you have not yet set up any connectors, you should see this:

![Airbyte - New connection](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/airbyte/create.png?w=2500&fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=64cf8588019046360048902c613af57d)

Airbyte - New connection

Click the button to set up a connection. Otherwise, click “ **New Connection** ” in the top right corner. From here, follow these steps:

Everything is now configured to pull your PlanetScale data into Airbyte and sync it to the selected destination on the schedule you chose. To run the connection, click “ **Connections** ” > “ **Launch** ”.

## Handling schema changes

Airbyte will not automatically detect when you make schema changes to your PlanetScale database. If you drop a column, your sync should throw an error as it looks for a column that doesn’t exist. However, if you add a column, the sync will continue without any errors. Airbyte will be unaware of the new column altogether. This is known as schema drift.

Whenever you perform a schema change, you need to notify Airbyte of it:

## Stopping Airbyte

At any point, you can disable any incremental or full syncs by going to the ‘Connection’ settings page and clicking ‘Delete this connection’. This will not touch any of the source or destination data, but will prevent Airbyte from doing any further operations.

![Airbyte - PlanetScale disconnection](https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/integrations/airbyte/delete.png?w=2500&fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=6f567c66276fef45ddc14267b48d496b)

Airbyte - PlanetScale disconnection

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
