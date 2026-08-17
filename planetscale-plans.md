---
url: https://planetscale.com/docs/planetscale-plans
title: "Planetscale Plans"
description: ""
access_date: 2026-08-17T20:08:47.652Z
current_date: 2026-08-17T20:08:47.652Z
---

## Overview

PlanetScale is built to accommodate all companies at all stages. Whether you need a hassle-free managed database for your side project or you’re running millions of queries per second at the scale of YouTube, we have a solution for you.

Our plans are split into two general offerings: [Base (self-serve)](#base-plan) and [Enterprise](#enterprise-plan).

## Base

Self-serve plan with fully-managed Vitess (MySQL-compatible) and Postgres databases. Features include sharding, branching, query insights, and more. Available with network-attached storage or Metal options.

[Learn more](#base-plan)

## Enterprise

Enhanced support, customizations, and deployment options. Available for both Vitess and Postgres with features like dedicated accounts, PCI compliance, and expert assistance.

[Learn more](#enterprise-plan)

## Base Plan

Our Base plan is completely self-serviceable. [Sign up for a PlanetScale account to get started](https://auth.planetscale.com/sign-up).

PlanetScale offers two database engines: **Vitess** (MySQL-compatible) and **Postgres**. Both are available with two storage options: **network-attached storage** and **Metal**.

- [**Network-attached storage**](plans/planetscale-skus.md#network-attached-storage) (Amazon Elastic Block Storage or Google Persistent Disk) databases come with autoscaling storage and have varying levels of compute power.
- [**Metal databases**](plans/planetscale-skus.md#metal) are backed by locally-attached NVMe drives for storage, unlocking incredible performance and cost-efficiencies. Because the drives are locally-attached, you need to choose both your compute and storage resources when you create your database.

Cluster size options are capped to `PS-160` and `M-320` SKUs until you have a successfully paid an invoice of at least $100.

If you need larger sizes immediately, please [contact us](https://planetscale.com/contact) to unlock all sizes.

On top of processing and memory, all **Base** cluster sizes share the following:

|  | **Vitess** | **Postgres** |
| --- | --- | --- |
| **Storage/month (network-attached storage)** | 10 GB included; $0.50 per instance per additional 1 GB\* | 10 GB included; additional storage pricing [varies by region and cloud provider](postgres/pricing.md#storage-pricing) |
| **Storage/month (Metal)** | Depends on selected NVMe drive size | Depends on selected NVMe drive size |
| **Available cluster sizes** | 22 | 22 |
| **Availability zones** | 3 | 3 |
| **Production branches** | 1 included\*\* | 1 included |
| **Development branches** | ~1,440 hours included (2× hours of current month) | Billed as a new cluster |
| **Concurrent Connections** | *Unmetered* | *Unmetered* |
| **Single node** | Not available | Available starting at $5/mo |
| **Query Insights retention** | 7 days | 7 days |
| **Horizontal sharding** | Included | [Coming soon](https://planetscale.com/blog/announcing-neki) |
| **Vertical sharding** | Included | Included |
| [**Deployment options**](plans/deployment-options.md) | Multi-tenant | Multi-tenant |
| **Read-only regions** | Available as an add-on | Coming soon |
| **Web console** | Included | Included |
| **PlanetScale CLI** | Included | Included |
| **Connection pooling** | [VTGates](vitess/scaling/vtgates.md) based on cluster size | [PgBouncer](postgres/connecting/pgbouncer.md) |
| [**Database Traffic Control®**](postgres/traffic-control.md) | Not available | Included |
| **SSO** | Available as an add-on\*\*\* | Available as an add-on\*\*\* |
| **Audit log retention** | 6 months | 6 months |
| **Private connections** | [AWS](vitess/connecting/private-connections.md) and [GCP](vitess/connecting/private-connections-gcp.md) | [AWS](postgres/connecting/private-connections/aws-privatelink.md) and [GCP](postgres/connecting/private-connections/gcp-private-service-connect.md) |
| **BAAs** | Included | Included |
| **Automatic backups** | Every 12 hours | Every 12 hours |
| **Support** | Standard, upgrade available\*\*\* | Standard, upgrade available\*\*\* |
| [**Data Branching®**](vitess/schema-changes/data-branching.md) | Included | Not available |

\* For HA network-attached storage databases, production branch storage is billed at $1.50/GB (1 primary + 2 replicas) and development branch storage is billed at $0.50/GB (1 primary).

\*\* Additional production branches are billed at the cost of your selected cluster size per month.

\*\*\* [SSO](security/sso.md) and [Business support](support.md#business) options are available on the Base plan for an additional fee.

### Additional production branches

Each HA production branch in the Base plan provisions a separate, production database cluster in our infrastructure. Upon adding an additional production branch, you will be prompted to select the cluster size for the new branch.

Each cluster size is priced based on the selected region. You can find the full list of pricing in our [Vitess cluster pricing documentation](plans/cluster-sizing.md).

If you have a `main` network-attached storage production branch using the **PS-40** cluster size and two additional production branches using the **PS-20** cluster size, the total cost for the database would be **$217.00** per month.

| **Production branch cluster** | **Cost per unit** | **Quantity** | **Total per month** |
| --- | --- | --- | --- |
| PS-40 | $99.00 | 1 | $99.00 |
| PS-20 | $59.00 | 2 | $118.00 |
| **Grand total** |  |  | **$217.00** |

Also note that pricing is prorated. If you create a new database in the middle of a billing cycle, you will only be charged for the appropriate fraction of the month. This also applies to changes to an existing database, such as upsizing. For example, if you have a database that started the month as a `PS-10` and at the halfway point in the month you upgrade to a `PS-20`, you would be charged $39/2 + $59/2 = $49 (assuming no additional other charges for storage, etc). The billing for the new sizes begins as soon as you begin the cluster resize.

### Development branches

Billing for development branches differs depending on whether you’re using PlanetScale Postgres or Vitess.

### Vitess development branches

Development branches, `PS-DEV` are billed for the time that they are running, prorated to the millisecond. Databases include `hours_in_current_month * 2` of development branch time per month (1,440 hours for a 30 day month) at no additional cost. Any time used over the included is billed at a rate of ~$0.014 per hour ($10 / hours\_in\_current\_month). You may see how many development branch hours have been used at any time by visiting your [organization billing page](https://app.planetscale.com/~/settings/billing/). Data is updated hourly.

### Postgres development branches

Postgres development branches, `PS-DEV`, are billed for the time that they are running, prorated to the millisecond. Each development branch is $5 per month. Development branches are not intended for HA production traffic, as they do not come with any replicas or have maintenance windows.

You can set spend email alerts from your billing page. See the [Spend management documentation](billing.md#spend-management) for more information.

If a database is created in the middle of a billing cycle, the included development branch hours are prorated. For example, if you create your database with 15 days remaining in the current month, the database will have `15 days * 2` (720 hours) included for that billing period.

### Fractional vCPU allocation

Some tiers of the Base plan indicate a fractional vCPU allocation. These branches run on our multi-tenant platform and this indicates the minimum number of cycles dedicated to your workload. The vast majority of the time, there are spare compute cycles available on the underlying machine instances hosting your branch, and those are available to be used by your workload as needed for no additional charge.

If you find the performance of a given query to be substantially inconsistent over the course of a given day, you may want to upgrade to a higher tier for more consistent performance.

## Enterprise plan

PlanetScale’s Enterprise plan is great for large-scale businesses who require the enterprise-level SLAs, want expert assistance through enterprise support, and would prefer to run in your own AWS or GCP account.

We offer many different deployment options, all of which come with the same set of standard features.

- **PlanetScale Single-tenant** runs in PlanetScale’s infrastructure with optional dedicated AWS/GCP accounts.
- [**PlanetScale Managed**](plans/managed.md) is a deployment option where your database runs entirely in your own AWS or GCP account, giving you complete control over your cloud infrastructure while still benefiting from PlanetScale’s fully managed service.

The table below covers those shared features, as well as the different options that vary depending on your chosen deployment.

|  | Single-tenant | **[PlanetScale Managed](plans/managed.md)** |
| --- | --- | --- |
| Customizable feature limits |  |  |
| [Support](support.md) | Business — Enterprise upgrade available | Enterprise |
| PCI compliant |  |  |
| Dedicated AWS/GCP account | Optional |  |
| Bring your own cloud (an AWS or GCP account *you* own) |  |  |
| Billing | Directly from PlanetScale | Partial payment through PlanetScale and infrastructure costs through AWS/GCP. Take advantage of your own discounts. Optionally, purchase through [AWS Marketplace](https://aws.amazon.com/marketplace/pp/prodview-luy3krhkpjne4). |

## How do I know if I need an Enterprise plan?

If you’re not sure whether your use case requires an Enterprise plan, we’re more than happy to chat with you to figure it out together. You can [fill out this form](https://planetscale.com/contact), and we’ll be in touch.

In general, if you need any of the following, Enterprise may be the best solution for you:

- Enhanced support — our expert team becomes an extension of your own. Additional options for technical account management, Slack-based support, and phone escalation.
- You need additional support to horizontally shard and migrate your database(s) to PlanetScale
- You need your database deployed in a single-tenant environment
- You need to keep your data in **your own** AWS or GCP account
- You need a PCI DSS certified service provider
- Mission-critical [response times](support.md#initial-response-times) including continuous support coverage
- Any other customizations — Our Enterprise plans offer a lot of flexibility, so if you have a requirement that’s not listed here, it’s best to [reach out](https://planetscale.com/contact) and we can see how we can help

## Plan add-ons

### Single Sign-on (SSO)

Add [Single Sign-on (SSO)](security/sso.md) for your organization to the Base plan for an additional fee of $199/month.

### Business support

Add [Business support](support.md#business) to the Base plan for an additional fee. See the Support page for more details.

### User-scheduled backups

On the Base plan, we run automated backups every 12 hours. Disk space for default backups is not counted against your plan’s storage limit.

You can also [schedule additional backups yourself](vitess/backups.md#create-manual-backups) as needed. For these additional user-scheduled backups, storage is billed at **$0.023 per GB** per month. Backups include system tables as well as your data and start at around 140MB.

## Free plan

PlanetScale does not offer a free plan, previously known as the “Developer” or “Hobby” plan. All databases require a paid subscription starting with our Base plan, which provides self-service access to fully-managed database clusters. Single-node Postgres databases are available starting at $5 per month.

## Scaler Pro plan

The Scaler Pro plan has been renamed to the Base plan.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
