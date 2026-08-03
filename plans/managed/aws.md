---
url: https://planetscale.com/docs/plans/managed/aws
title: "Aws"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale Managed on AWS overview

> PlanetScale Managed on Amazon Web Services (AWS) is a single-tenant deployment of PlanetScale in your AWS organization within an isolated AWS Organizations member account.

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

<PlatformAvailability current="both" />

## Overview

In this configuration, you can use the same API, CLI, and web interface that PlanetScale offers, with the benefit of running entirely in an AWS Organizations member account that you own and PlanetScale manages for you.

## Architecture

The PlanetScale data plane is deployed inside of a PlanetScale-controlled AWS Organizations member account in your AWS organization.
The database cluster will run within this member account, orchestrated via Kubernetes. PlanetScale Managed supports MySQL-compatible Vitess and Postgres clusters.

We distribute components of the cluster across three AWS availability zones within your selected region to ensure high availability.
You can deploy PlanetScale Managed to any AWS region with at least three availability zones, including those not supported by the PlanetScale self-serve product.

Backups, part of the data plane, are stored in S3 inside the same member account.
PlanetScale Managed uses isolated Amazon Elastic Compute Cloud (Amazon EC2) instances as part of the deployment.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/aws-arch-diagram.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=36a9f1e69370e041a7ee576e3f0f42ba" alt="Architecture diagram for PlanetScale Managed in AWS" width="1664" height="1118" data-path="images/assets/docs/managed/aws/aws-arch-diagram.png" />
</Frame>

Your database lives entirely inside a dedicated AWS Organizations member account within your AWS organization.
PlanetScale will not have access to other member accounts nor your organization-level settings within AWS.
Outside of your AWS organization, we run the PlanetScale control plane, which includes the PlanetScale API and web application, including the dashboard you see at `app.planetscale.com`.

In a MySQL deployment, the Vitess cluster running inside Kubernetes is composed of a number of Vitess components.
All incoming queries are received by one of the **VTGates**, which then routes them to the appropriate **VTTablet**.
The VTGates, VTTablets, and MySQL instances are distributed across 3 availability zones.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/aws/aws-vitess.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=8963ce811fda1505db16f995ea7d0d26" alt="Diagram of Vitess cluster on AWS" width="2184" height="1626" data-path="images/assets/docs/managed/aws/aws-vitess.png" />
</Frame>

Several additional required Vitess components are run in the Kubernetes cluster as well.
The topology server keeps track of cluster configuration.
**VTOrc** monitors cluster health and handles repairs, including managing automatic failover in case of an issue with a primary.
**vtctld** along with the client **vtctl** can be used to make changes to the cluster configuration and run workflows.

In a Postgres deployment, the architecture uses a dedicated PostgreSQL cluster with similar high-availability and distribution across availability zones.

## Security and compliance

PlanetScale Managed is an excellent option for organizations with specific security and compliance requirements.

You own the AWS organization and member account that PlanetScale is deployed within an isolated architecture. This differs from when your PlanetScale database is deployed within our AWS organizations.

<Note>
  PlanetScale manages the entire member account and can NOT support customers running Terraform or other configuration management in the member account.
</Note>

### PCI compliance

Along with System and Organization Controls (SOC) 2 Type 2 and other [security and compliance](../../security.md) practices that PlanetScale has been issued and follows, PlanetScale Managed has been issued an Attestation of Compliance (AoC) and Report on Compliance (RoC), certifying our compliance with the PCI DSS 4.0 as a [Level 1 Service Provider](https://www.pcisecuritystandards.org/glossary/service-provider/). This enables PlanetScale Managed to be used via a shared responsibility model across merchants, acquirers, issuers, and other roles in storing and processing cardholder data.

<Note>
  If you have any questions or concerns related to the security and compliance of PlanetScale Managed, please [contact us](https://planetscale.com/contact), and we will be happy to discuss them further.
</Note>

### Private database connectivity

By default, all database connections are encrypted but use public endpoints. [AWS PrivateLink](aws/privatelink.md) and [AWS VPC peering](aws/vpc-peering.md) keep database traffic on private AWS networks, but they use different network models.

|                   | AWS PrivateLink                                                                           | AWS VPC peering                                                                     |
| ----------------- | ----------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| Connectivity      | Exposes a specific service through interface endpoints                                    | Routes private traffic directly between two VPCs                                    |
| CIDR requirements | Overlapping VPC CIDR blocks are supported                                                 | VPC CIDR blocks must not overlap                                                    |
| Routing           | No route to the service VPC is required                                                   | Both VPC owners maintain routes                                                     |
| Access scope      | Connectivity is limited to the endpoint service                                           | Routes and security controls determine which resources can communicate              |
| Topology          | Each VPC uses its own interface endpoint                                                  | One-to-one and non-transitive; each additional VPC needs its own peering connection |
| DNS               | Uses PrivateLink private DNS                                                              | Requires a cross-account private hosted zone association                            |
| AWS charges       | Hourly charges per interface endpoint and Availability Zone, plus data processing charges | No hourly peering connection charge; data transfer charges can apply                |

AWS PrivateLink is the recommended default because it exposes only the database endpoint and does not require non-overlapping CIDR blocks. VPC peering can be useful when your architecture requires direct, routed connectivity between the application VPC and the Managed VPC. Contact your PlanetScale Solutions Engineer to choose an option.

See the AWS documentation for current [AWS PrivateLink pricing](https://aws.amazon.com/privatelink/pricing/) and [VPC peering pricing](https://aws.amazon.com/vpc/pricing/).

### Fully private network isolation

You can also turn off public database access with a dual AWS PrivateLink setup. PlanetScale's control plane will talk to your member account over AWS PrivateLink, and your VPCs will also communicate with your database over AWS PrivateLink. Please get in touch with your PlanetScale Account Manager for more information on how to set up fully private network isolation.

### Third-account customer-controlled public key infrastructure

PlanetScale Managed on AWS supports public key infrastructure (PKI) services. PlanetScale Managed customers can provide PlanetScale the use of a set of customer-managed keys in a third AWS account inside your organization. This third account is controlled by you, the customer. PlanetScale has no administrative access. Your organization is the custodian for this key material. PlanetScale uses the customer-managed keys to encrypt EBS volumes, S3 buckets, and for envelope encryption of backups.

## Billing

With any of the PlanetScale Enterprise offerings, including PlanetScale Managed, you have the option to purchase PlanetScale through the [AWS Marketplace](https://aws.amazon.com/marketplace/pp/prodview-luy3krhkpjne4). In addition to this, the resources you use on PlanetScale will qualify against your EDP commitment.

<Note>
  If you have any billing-related questions for PlanetScale Managed, please [contact us](https://planetscale.com/contact), and we will be happy to discuss them further.
</Note>

## Getting started with PlanetScale Managed in AWS

If you want to see what is involved in getting set up with PlanetScale Managed in AWS, you can see the [AWS set up documentation](aws/getting-started.md).

If you are interested in exploring PlanetScale Managed further, please [contact us](https://planetscale.com/contact), and we can chat more about your requirements and see if PlanetScale Managed is a good fit for you.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
