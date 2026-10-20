---
url: https://planetscale.com/docs/plans/managed/azure/getting-started
title: "Getting Started"
description: ""
access_date: 2026-10-20T18:01:41.586Z
current_date: 2026-10-20T18:01:41.586Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Set up PlanetScale Managed in Azure

> Prepare a dedicated Azure subscription and deploy the one-time bootstrap required for PlanetScale Managed.

## Overview

This guide explains how to prepare an Azure subscription for PlanetScale Managed and deploy the one-time authorization bootstrap.

> [!NOTE]
>
> This guide is intended for PlanetScale Managed customers currently working with the PlanetScale team. You cannot complete provisioning without PlanetScale enabling Managed for your organization. If you are interested in [PlanetScale Managed](../../managed.md), [contact us](https://planetscale.com/contact).

## Step 1: Prepare a dedicated Azure subscription

Create a new Azure subscription in your Microsoft Entra tenant. The subscription must:

* Be attached to an active Azure billing account
* Be dedicated to one PlanetScale organization
* Contain no existing workloads or customer-managed infrastructure
* Allow resource deployment in the Azure regions selected with your Solutions Engineer

Azure VM SKU availability varies by subscription and region. Coordinate region selection with your Solutions Engineer. PlanetScale may ask you to verify that the dedicated subscription has access to the required VM SKUs and sufficient quota across three availability zones in each requested region.

The person deploying the bootstrap must have the Azure **Owner** role on the subscription. Owner is required because the template creates resources, managed identities, a custom role definition, and role assignments.

Once PlanetScale begins managing the subscription, do not modify its resources or use your own infrastructure-as-code tooling inside it. [Contact support](https://planetscale.com/contact?initial=support) to discuss changes or customization.

## Step 2: Deploy the PlanetScale bootstrap

The PlanetScale bootstrap is a subscription-scoped Azure Resource Manager (ARM) template generated from a reviewed Bicep source. It has no input parameters and creates no database infrastructure.

[Deploy the PlanetScale bootstrap to Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fplanetscale.com%2Fdocs%2Fassets%2Fmanaged%2Fazure%2Fazure-mn-bootstrap.json)

You can also [download the ARM template](https://planetscale.com/docs/assets/managed/azure/azure-mn-bootstrap.json) for review before deployment.

### Deploy through the Azure portal

**Step 1**

Open the **Deploy the PlanetScale bootstrap to Azure** link and sign in with the account that has Owner on the dedicated subscription.

**Step 2**

Select the dedicated subscription. Select the deployment region agreed upon with your PlanetScale Solutions Engineer.

**Step 3**

Review the template and select **Create**. The template does not require any parameters.

**Step 4**

Wait for the subscription deployment to reach **Succeeded**.

### Deploy with the Azure CLI

Download the template and deploy it at subscription scope:

```bash theme={null}
az deployment sub create \
  --name planetscale-managed-bootstrap \
  --location <azure-region> \
  --template-file azure-mn-bootstrap.json
```

Replace `<azure-region>` with the primary deployment region agreed upon with your Solutions Engineer.

### Resources and permissions created

The template creates the `planetscale-bootstrap` resource group and three user-assigned managed identities:

| Identity | Initial authorization | Purpose |
| - | - | - |
| Planner | Reader | Read Azure resources during Terraform planning |
| Runner | Contributor, Role Based Access Control Administrator, and a custom role for managing role definitions | Provision resources and manage authorization within the dedicated subscription |
| Support | None | Reserved for scoped operational access configured by PlanetScale after onboarding |

The planner and runner identities use federated credentials. The template creates no passwords, client secrets, or database resources.

> [!WARNING]
>
> The runner's combined permissions are intentionally equivalent to Owner within the dedicated subscription. The subscription is the PlanetScale Managed security boundary and must not contain unrelated resources.

## Step 3: Collect the deployment output

The deployment outputs the subscription and Microsoft Entra tenant identifiers plus the resource, client, and principal IDs of all three managed identities. These values are identifiers, not credentials or secrets.

In the Azure portal:

1. Open the dedicated **Subscription**.
2. Select **Deployments**.
3. Open the successful bootstrap deployment.
4. Select **Outputs** and copy the `managed` output.

If you deployed with the Azure CLI, retrieve the same output with:

```bash theme={null}
az deployment sub show \
  --name planetscale-managed-bootstrap \
  --query properties.outputs.managed.value \
  --output json
```

## Step 4: Send the onboarding information to PlanetScale

Send your Solutions Engineer:

* The name and public ID of your PlanetScale organization
* The complete `managed` deployment output
* The Azure region or regions selected for the deployment

Do not send Azure credentials or create client secrets for PlanetScale.

## Step 5: PlanetScale validates access and provisions the deployment

PlanetScale validates planner and runner federation, confirms the bootstrap identities and role assignments, and then provisions the Managed data plane.

PlanetScale may work with you to request regional Azure quota increases based on the selected database products and expected capacity. Do not restrict quotas or resource providers after provisioning begins.

Your Solutions Engineer will notify you when provisioning is complete and your team can create databases through the PlanetScale dashboard, API, CLI, or [MCP](../../../mcp-server.md).

## Step 6: Verify the deployment

After PlanetScale confirms provisioning is complete:

1. Sign in to the [PlanetScale dashboard](https://app.planetscale.com).
2. Confirm that the Managed region is available to your organization.
3. Create or select a database in that region.
4. Verify a TLS connection through the public endpoint.
5. If private connectivity is required, contact your Solutions Engineer.

> [!NOTE]
>
> Private database connectivity is available through [Azure Private Link](private-link.md) or Azure VNet peering (coming soon). Contact your Solutions Engineer to choose an option before provisioning.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
