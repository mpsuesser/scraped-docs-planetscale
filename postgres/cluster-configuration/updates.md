---
url: https://planetscale.com/docs/postgres/cluster-configuration/updates
title: "Updates"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Cluster updates

> PlanetScale occasionally releases software updates for your Postgres cluster.

This page covers Postgres.

## How to update your cluster

To check if your cluster has any available updates, go to the **Clusters** page in your PlanetScale dashboard and look for the "Cluster update available" indicator.

> [!NOTE]
>
> If you make any other cluster changes that require a cluster restart, such as enabling an extension, we will roll out the latest updates during that cluster restart.

To update your cluster:

**Step 1**

Click the **"Queue update"** button on your Clusters page. This will queue the updates without immediately starting the update process.

**Step 2**

You should see "This cluster has queued changes". You can batch additional cluster changes before applying them if needed.

**Step 3**

Once you're ready, click **"Apply changes"**.

**Step 4**

In the confirmation modal, review the warning that applying changes will close any open connections. Make sure your application is equipped to handle closed connections, then click **"Apply changes"** to confirm.

**Step 5**

You can view the status of your changes under the **Changes** tab.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
