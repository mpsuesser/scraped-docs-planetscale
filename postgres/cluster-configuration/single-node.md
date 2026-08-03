---
url: https://planetscale.com/docs/postgres/cluster-configuration/single-node
title: "Single Node"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

Single node is only available for network-attached storage databases. You cannot create a single node [Metal](../../metal.md) database.

Single node databases are available on all network-attached storage cluster sizes and begin at $5/month. For full single node pricing, see the [pricing](https://planetscale.com/pricing) page.

To create a single node database, just select “ **Single node** ” and your cluster size during the database creation process.

## Resizing your single node database

You can upsize or downsize your single node Postgres database by going to “ **Clusters** ”, selecting the cluster size from the dropdown, and clicking “ **Queue instance changes** ”, “ **Apply changes** ”. During the resize, we will surge a new replica on the selected size, sync your data, promote the replica to primary, and decommission the old node.

## Switch from single node to HA

To upgrade your single node database to an HA cluster, go to the Clusters page, select the “HA Primary + Multi-replica” option, choose your cluster size from the dropdown (network-attached storage or [Metal](../../metal.md)), click “Queue instance changes”, and click “Apply changes”.

During this process, 2 additional replicas across 3 Availability Zones are added to your database cluster. If you selected a Metal cluster, your data is copied over to the new instances with locally-attached storage. It follows the process described in the [Upgrading to a Metal database documentation](../../metal/create-a-metal-database.md#upgrading-an-existing-database-to-metal).

## Switch from HA to single node

To switch an HA database with 1 primary and 2+ replicas to single node, go to your Clusters page, select “Single node”, choose a cluster size, click “Queue instance changes”, and click “Apply changes”.

This will remove any existing replicas you have and leave you with a single non-HA primary.
