---
url: https://planetscale.com/docs/plans/managed/azure
title: "Azure"
description: ""
access_date: 2026-10-13T18:20:34.341Z
current_date: 2026-10-13T18:20:34.341Z
---

## Overview

PlanetScale Managed on Microsoft Azure runs the PlanetScale data plane in a dedicated Azure subscription that you own and PlanetScale operates. You continue to use the PlanetScale dashboard, API, and CLI while your database infrastructure and data remain in your Azure environment.

## Architecture

PlanetScale deploys the data plane to a dedicated subscription in your Microsoft Entra tenant. The database runs on Azure Kubernetes Service (AKS), distributed across three availability zones in regions that support them.

The subscription also contains the networking, managed identities, Azure Key Vaults, Azure Container Registry, storage accounts, and other resources required to operate the deployment. Backups are encrypted and stored in Azure Blob Storage in the same subscription.

PlanetScale’s control plane runs outside your Azure subscription. It provides the PlanetScale dashboard, API, automation, and operational workflows used to manage your databases.

PlanetScale receives access only to the dedicated subscription. It does not receive administrative access to your Microsoft Entra tenant or other Azure subscriptions.

## Subscription ownership and management

The subscription must be dedicated to PlanetScale Managed and must not contain existing workloads. You retain ownership of the subscription, Microsoft Entra tenant, and Azure billing relationship.

PlanetScale manages all resources inside the subscription after the initial bootstrap. Do not modify, remove, or manage those resources with your own infrastructure-as-code tooling. [Contact support](https://planetscale.com/contact?initial=support) to discuss configuration changes or customization.

The one-time [Azure bootstrap](azure/getting-started.md) creates customer-owned managed identities for PlanetScale automation and support. The bootstrap uses federated credentials rather than client secrets.

## Security and compliance

The dedicated subscription is the security and operational boundary for the deployment. PlanetScale automation receives the subscription-scoped permissions required to provision and operate the data plane. PlanetScale does not require tenant-wide Microsoft Entra administrator roles.

PlanetScale Managed is designed for organizations with additional security, compliance, and isolation requirements. PlanetScale maintains [security and compliance](../../security.md) programs including SOC 2 Type 2 and PCI DSS 4.0 Level 1 Service Provider compliance.

If you have questions about the security or compliance of PlanetScale Managed on Azure, contact your PlanetScale Solutions Engineer.

## Private database connectivity

Database connections use public, TLS-encrypted endpoints by default. PlanetScale Managed on Azure supports private connectivity through [Azure Private Link](azure/private-link.md).

Azure Private Link exposes only the database service through a customer-owned Private Endpoint. It does not provide routed access between the PlanetScale Managed virtual network and your application virtual networks.

VNet peering is not currently supported. Contact your Solutions Engineer if you need VNet peering. Non-overlapping IP ranges between the PlanetScale Managed subscription and any peered application subscriptions are required. The IP ranges to use in the PlanetScale subscription must be coordinated with your Solutions Engineer before PlanetScale provisions resources in the managed Azure subscription.

## Billing

Azure bills the resources in the dedicated Managed subscription directly to your Azure billing account. Azure also bills Private Endpoint resources to the subscription where you create them.

PlanetScale pricing is defined in your Enterprise agreement. Contact your PlanetScale account team with billing questions.

## Get started

Follow the [Azure setup guide](azure/getting-started.md) to prepare a dedicated subscription and grant PlanetScale access.

If you are interested in PlanetScale Managed on Azure, [contact us](https://planetscale.com/contact) to discuss your requirements.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
