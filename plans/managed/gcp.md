---
url: https://planetscale.com/docs/plans/managed/gcp
title: "Gcp"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale Managed on GCP overview

> PlanetScale Managed on Google Cloud Platform (GCP) is a single-tenant deployment of PlanetScale in your GCP organization within an isolated project.

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

In this configuration, you can use the same API, CLI, and web interface that PlanetScale offers, with the benefit of running entirely in a GCP project that you own and PlanetScale manages for you.

## Architecture

As you can see in the architecture diagram below, the PlanetScale data plane is deployed inside of a PlanetScale-controlled project in your GCP organization.
The database cluster will run within this project, orchestrated by Kubernetes. PlanetScale Managed supports MySQL-compatible Vitess and Postgres clusters.

We distribute components of the cluster across three GCP zones within a region to ensure high availability.
You can deploy PlanetScale Managed to any GCP region with at least three zones, including zones not supported by the PlanetScale self-serve product, so long as the region supports the required GCP services (including but not limited to Google Compute Engine (GCE), Google Kubernetes Engine (GKE), Cloud Storage, Persistent Disk, Cloud Key Management Service (Cloud KMS), Cloud Logging).

Backups, part of the data plane, are stored in Cloud Storage inside the same project.
PlanetScale Managed uses isolated GCE instances as part of the deployment.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/gcp/gcp-arch-diagram.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=6a6894b126235de690bce817ccda0dcf" alt="Architecture diagram for PlanetScale Managed in GCP" width="1664" height="1118" data-path="images/assets/docs/managed/gcp/gcp-arch-diagram.png" />
</Frame>

Your database lives entirely inside a dedicated project within GCP. PlanetScale will not have access to other projects nor your organization-level settings within GCP. Outside of your GCP organization, we run the PlanetScale control plane, which includes the PlanetScale API and web application, including the dashboard you see at `app.planetscale.com`.

In a MySQL deployment, the Vitess cluster running inside Kubernetes is composed of a number of Vitess components.
All incoming queries are received by one of the **VTGates**, which then routes them to the appropriate **VTTablet**.
The VTGates, VTTablets, and MySQL instances are distributed across 3 availability zones.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/managed/gcp/gcp-vitess.png?fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=8834fe7fca2553cb21c2b3d5b66b4fb3" alt="Diagram of Vitess cluster on GCP" width="2184" height="1626" data-path="images/assets/docs/managed/gcp/gcp-vitess.png" />
</Frame>

Several additional required Vitess components are run in the Kubernetes cluster as well.
The topology server keeps track of cluster configuration.
**VTOrc** monitors cluster health and handles repairs, including managing automatic failover in case of an issue with a primary.
**vtctld** along with the client **vtctl** can be used to make changes to the cluster configuration and run workflows.

In a Postgres deployment, the architecture uses a dedicated PostgreSQL cluster with similar high-availability and distribution across availability zones.

## Security and compliance

PlanetScale Managed is an excellent option for organizations with specific security and compliance requirements.

You own the GCP organization and project that PlanetScale is deployed within an isolated architecture. This differs from when your PlanetScale database is deployed within our GCP organizations.

Along with System and Organization Controls (SOC) 2 Type 2 and PlanetScale [security and compliance](../../security.md) practices that PlanetScale has been issued and follows, we can also sign BAAs for [HIPAA compliance](https://planetscale.com/blog/planetscale-and-hipaa) on PlanetScale Managed.

<Note>
  PlanetScale manages the entire project and can NOT support customers running Terraform or other configuration management in the project.
</Note>

### GCP Private Service Connect

By default, all connections are encrypted, but public. Optionally, you also have the option to use private database connectivity through [GCP Private Service Connect](gcp/private-service-connect.md), which is only available on single-tenancy deployment options, including PlanetScale Managed.

<Note>
  If you have any questions or concerns related to the security and compliance of PlanetScale Managed, please [contact us](https://planetscale.com/contact), and we will be happy to discuss them further.
</Note>

## Getting started with PlanetScale Managed in GCP

If you want to see what is involved in getting set up with PlanetScale Managed in GCP, you can see the [GCP set up documentation](gcp/getting-started.md).

If you are interested in exploring PlanetScale Managed further, please [contact us](https://planetscale.com/contact), and we can chat more about your requirements and see if PlanetScale Managed is a good fit for you.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
