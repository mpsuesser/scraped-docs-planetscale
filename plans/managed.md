---
url: https://planetscale.com/docs/plans/managed
title: "Managed"
description: ""
access_date: 2026-10-30T18:26:51.105Z
current_date: 2026-10-30T18:26:51.105Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale Managed overview

> Run PlanetScale in your own AWS, GCP, or Azure account, operated by our team with your data in your cloud.

Available for Vitess, Neki, and Postgres.

## What is PlanetScale Managed?

PlanetScale Managed is a single-tenant deployment of PlanetScale within your Amazon Web Services (AWS), Google Cloud Platform (GCP), or Microsoft Azure account. In this configuration, you can use the same API, CLI, and web interface that PlanetScale offers, with the benefit of running entirely in your own cloud account.

We have packaged the best parts of PlanetScale into a container and can deploy and operate them in your own account, bringing you the best of SaaS with the added benefit of a deployment free of noisy neighbors, enhanced support, and additional security guarantees.

With PlanetScale Managed, it is more than just an on-premises deployment of your database; you are getting the PlanetScale expert team operating your database alongside your team for a *truly* fully managed database solution. The PlanetScale team is on-call for your databases.

## How does PlanetScale Managed work?

PlanetScale Managed is a packaged [data plane](https://en.wikipedia.org/wiki/Data_plane), deployed to an AWS Organizations member account, GCP project, or Azure subscription that you own and we operate. PlanetScale Managed supports [Vitess](https://planetscale.com/docs/vitess.md), [Neki](https://planetscale.com/docs/neki.md), and [Postgres](https://planetscale.com/docs/postgres.md). Existing Managed customers should [contact us](https://planetscale.com/contact) to enable Neki. For MySQL databases, the data plane is built on [Vitess and Kubernetes](https://planetscale.com/blog/scaling-hundreds-of-thousands-of-database-clusters-on-kubernetes). For Postgres databases, it runs a dedicated PostgreSQL cluster orchestrated by Kubernetes. Your database lives entirely inside a dedicated account, project, or subscription within your cloud organization. PlanetScale will not have access to other accounts, projects, or subscriptions nor your organization-level settings within the cloud service provider. At the same time, you still get to interact with your databases through the web application, pscale CLI, or the PlanetScale API, as you usually would with our hosted product. This includes developer experience features such as non-blocking schema changes, safe migrations, database branching, query insights, and more.

If you are an existing PlanetScale user, moving to PlanetScale Managed requires no changes to your existing developer workflows.

The database is deployed in a single-tenant environment and isolated in an AWS Organizations member account, GCP project, or Azure subscription from the rest of your organization's infrastructure. By default, all connections are encrypted, but public. PlanetScale Managed supports private database connectivity through [AWS PrivateLink](https://planetscale.com/docs/plans/managed/aws/privatelink.md), [GCP Private Service Connect](https://planetscale.com/docs/plans/managed/gcp/private-service-connect.md), or [Azure Private Link](https://planetscale.com/docs/plans/managed/azure/private-link.md). Private connections are also available for supported databases on the [Base plan](https://planetscale.com/docs/planetscale-plans.md#base-plan).

Read more about PlanetScale Managed on each cloud provider:

> [card] [PlanetScale Managed on Amazon Web Services](https://planetscale.com/docs/plans/managed/aws.md)

> [card] [PlanetScale Managed on Microsoft Azure](https://planetscale.com/docs/plans/managed/azure.md)

> [card] [PlanetScale Managed on Google Cloud Platform](https://planetscale.com/docs/plans/managed/gcp.md)

## Benefits of PlanetScale Managed

Single-tenancy is one of many benefits when it comes to PlanetScale Managed. Still, with this PlanetScale Enterprise service, you also get:

* Assistance with [horizontal sharding](https://planetscale.com/docs/vitess/sharding.md) for MySQL and [Neki](https://planetscale.com/docs/neki.md) for Postgres
* Deployment to additional regions
* [PCI compliance](https://planetscale.com/docs/security.md#pci-compliance)
* Additional [support options](https://planetscale.com/docs/support.md#enterprise)
* Available on [AWS Marketplace](https://aws.amazon.com/marketplace/pp/prodview-luy3krhkpjne4). Your PlanetScale purchase through the AWS Marketplace and the resources you use on PlanetScale will qualify against your EDP commitment.

## How do I get PlanetScale Managed?

If you are interested in seeing if PlanetScale Managed fits your needs, [contact us](https://planetscale.com/contact), and we can chat more about your requirements and see if PlanetScale Managed is a good fit for you.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
