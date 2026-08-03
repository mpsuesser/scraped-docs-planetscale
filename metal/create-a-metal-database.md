---
url: https://planetscale.com/docs/metal/create-a-metal-database
title: "Create A Metal Database"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

[PlanetScale Metal](../metal.md) databases can be created in a similar way to other PlanetScale databases. However, there are a few important things to keep in mind when creating a new Metal database or upgrading an existing database to Metal, which will be covered here.

## Create a new Metal database

After logging in to `app.planetscale.com`, click “New database” -> “Create new database.” Next, enter the name of your new database and select the “PlanetScale Metal” option.

![New Metal database](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/metal-new-db.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=401d06c40e5d73e239eb11e7be0edb8b)

New Metal database

![New Metal database](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/metal-new-db-darkmode.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=a9b0f878e02187709ddec1ab376656d5)

New Metal database

This brings up a set of options to choose from for the size of your Metal database. Start by choosing the vCPU and RAM combination that best suits your needs, then use the dropdown to select the drive size for the instance.

![Select Metal database size](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/metal-new-db-choose-size.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=1448196260f2a6355c1398cc0da437bd)

Select Metal database size

![Select Metal database size](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/metal-new-db-choose-size-darkmode.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=bbb34e4cafd1cb2daa6de32e27e56546)

Select Metal database size

As opposed to [network-attached storage](../plans/planetscale-skus.md#network-attached-storage) databases, [Metal](../plans/planetscale-skus.md#metal) databases do not autoscale their storage size. Therefore, it’s important to make a good size choice from the start. If you are starting a new project from scratch on a Metal database and you do not expect massive initial growth, it is likely best to choose the smallest drive possible. If you intend to migrate an existing database into this in the near future, ensure that your drive will fit all of the data while also allowing room for further growth.

When ready, click “Create database.” After database initialization completes, you can begin using the database.

## Upgrading an existing database to Metal

You can also upgrade an existing database cluster to Metal. This is a no-downtime operation.

![Upgrade cluster to Metal](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/metal-upgrade.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=435d75638af28a3443f99f2c09726d72)

Upgrade cluster to Metal

![Upgrade cluster to Metal](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/metal-upgrade-darkmode.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=2c0dc9a30e381dbe0cdeab171283de53)

Upgrade cluster to Metal

Keep in mind that this is not an immediate operation. If you have a large database, it may take a while for the upgrade to complete since behind the scenes, your entire database needs to be migrated to the new NVMe drives. Ensure that you upgrade well before reaching max drive capacity. We recommend upgrading at no later than 75% in most cases, and even earlier than that if you are growing quickly.

## Monitoring Metal storage

The storage for Metal databases does not autoscale. It is important to keep a close eye on the storage capacity of Metal databases, and upgrade well before running out of space.

There are several ways to monitor this.

You can view storage information on the right side of the main PlanetScale dashboard for Metal databases.

After the upgrade is complete, we recommend going to the Insights page and view your query latency diagrams. If you transitioned from a network-attached storage cluster to a Metal one of the same size, you should see a reduction in query latency.

![Metal storage info](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/storage.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=8b3845290a5dc35803a5880b7d88df8c)

Metal storage info

![Metal storage info](https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metal/storage-darkmode.png?w=2500&fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=702d11f23406669e4a78444d12dcf1b8)

Metal storage info

You should make a habit of regularly logging in and checking the health of your database, keep an eye on this number. If PlanetScale detects that you have only 6GiB or less of available storage, it will cause your database to reject writes, preferring to keep the database available rather than cause a total system failure due to running out of storage. This is a safety measure put in place to protect your data. You should upgrade to a larger instance long before reaching this point. You can upgrade to a larger Metal instance / drive using the same set of steps described above.

Additionally, operations such as deploy requests may not run if you do not have enough storage. The exception to this is if you are performing an [instant deployment](../vitess/schema-changes/deploy-requests.md#instant-deployments).

Deploying online schema changes with VReplication requires that we [make a copy of the affected tables](../vitess/schema-changes/how-online-schema-change-tools-work.md#initializing-the-ghost-table-schema). If you are nearing max capacity or making a change on a very large table, you risk not having enough storage to begin the online schema change. We will let you know that there is not enough space to create a deploy request in these cases.

It is critical to upgrade your instance storage size well before you are nearing max capacity. We will sent you email notices when your database storage reaches the following thresholds: 60%, 75%, 85%, 90%, 95%. We will also email you when we estimate that your storage will run out in 1 week and 24 hours, based on recent usage trends.

The exact point at which you should upgrade depends on your data growth rate, drive size, and other factors. We recommend upgrading no later than at 75% capacity, and even before that in some cases of fast growth. Upgrading to a larger drive takes time, as it requires copying your database to new drives, so it’s important to upgrade well before hitting max capacity.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
