---
url: https://planetscale.com/docs/plans/managed/azure/private-link
title: "Private Link"
description: ""
access_date: 2026-10-20T18:01:41.586Z
current_date: 2026-10-20T18:01:41.586Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Set up Azure Private Link with PlanetScale Managed

> Connect an application virtual network privately to a PlanetScale Managed deployment in Azure.

[Azure Private Link](https://learn.microsoft.com/azure/private-link/private-link-overview) connects an application virtual network to your PlanetScale Managed deployment without sending database traffic over the public internet.

## How the connection works

PlanetScale operates an Azure Private Link Service in your Managed subscription. You create a Private Endpoint in an application VNet and manage the endpoint's private IP address and DNS.

PlanetScale provides:

* The Private Link Service alias
* Confirmation that each application subscription is allowed to discover and connect to the service

You manage:

* The Private Endpoint in the application subscription
* Network access from your applications to the endpoint
* Optional private DNS zones and DNS records

> [!WARNING]
>
> The Private Link Service alias identifies the Azure service. It is not a database hostname and cannot be used in a connection string.

## Prerequisites

Before you begin, you need:

* A [provisioned PlanetScale Managed deployment on Azure](getting-started.md)
* The Azure subscription and VNet where the applications that will connect to PlanetScale run
* A subnet in that VNet where Azure can create the Private Endpoint
* Permission to create Private Endpoints and network interfaces
* The `Microsoft.Network` resource provider registered in the application subscription
* Network access from your applications to the database ports supplied by PlanetScale
* If you plan to use private DNS, permission to create Private DNS zones, VNet links, and DNS records

## Step 1: Ask PlanetScale to enable the application subscription

Give your PlanetScale Solutions Engineer the subscription ID for each Azure subscription where you will create a Private Endpoint.

PlanetScale configures the Private Link Service and gives you:

* A Private Link Service alias, such as `SERVICE.GUID.REGION.azure.privatelinkservice`
* Confirmation that your application subscription is configured for automatic approval

If your deployment spans multiple Azure regions, PlanetScale provides a separate service alias for each region. Create a Private Endpoint for each region your applications use.

> [!WARNING]
>
> Wait for PlanetScale to confirm that the application subscription is configured before creating the endpoint. Azure does not retroactively auto-approve an endpoint created before its subscription was added.

## Step 2: Create a Private Endpoint

You can create the endpoint in the Azure portal or with the Azure CLI.

### Azure portal

**Step 1**

In the Azure portal, open **Private Link Center**, select **Private endpoints**, and then select **Create**.

**Step 2**

Select the application subscription and resource group where you want to create the endpoint. Enter an endpoint name and select its region.

**Step 3**

On the resource page, select the option to connect by resource ID or alias. Enter the Private Link Service alias supplied by PlanetScale.

**Step 4**

Select the VNet and subnet used by your applications. Complete the endpoint creation without configuring automatic DNS integration.

**Step 5**

Open the new endpoint and confirm that its connection status is **Approved**. Record the private IP address assigned to its network interface.

### Azure CLI

Set values for your environment and retrieve the endpoint subnet ID:

```bash theme={null}
ENDPOINT_RESOURCE_GROUP="<private-endpoint-resource-group>"
LOCATION="<azure-region>"
VNET_RESOURCE_GROUP="<vnet-resource-group>"
VNET_NAME="<vnet-name>"
SUBNET_NAME="<subnet-name>"
ENDPOINT_NAME="<private-endpoint-name>"
PLS_ALIAS="<private-link-service-alias-from-planetscale>"

SUBNET_ID=$(az network vnet subnet show \
  --resource-group "$VNET_RESOURCE_GROUP" \
  --vnet-name "$VNET_NAME" \
  --name "$SUBNET_NAME" \
  --query id \
  --output tsv)
```

Create the endpoint by using the service alias and the manual-request flow. Azure automatically approves the request when PlanetScale has configured the application subscription:

```bash theme={null}
az network private-endpoint create \
  --resource-group "$ENDPOINT_RESOURCE_GROUP" \
  --name "$ENDPOINT_NAME" \
  --location "$LOCATION" \
  --subnet "$SUBNET_ID" \
  --private-connection-resource-id "$PLS_ALIAS" \
  --connection-name "$ENDPOINT_NAME" \
  --manual-request true \
  --request-message "PlanetScale private connection"
```

Confirm that the connection is approved:

```bash theme={null}
az network private-endpoint show \
  --resource-group "$ENDPOINT_RESOURCE_GROUP" \
  --name "$ENDPOINT_NAME" \
  --query 'manualPrivateLinkServiceConnections[0].privateLinkServiceConnectionState.status' \
  --output tsv
```

The expected result is `Approved`.

Get the endpoint's private IP address:

```bash theme={null}
NIC_ID=$(az network private-endpoint show \
  --resource-group "$ENDPOINT_RESOURCE_GROUP" \
  --name "$ENDPOINT_NAME" \
  --query 'networkInterfaces[0].id' \
  --output tsv)

PRIVATE_ENDPOINT_IP=$(az network nic show \
  --ids "$NIC_ID" \
  --query 'ipConfigurations[0].privateIPAddress' \
  --output tsv)
```

## Step 3: Choose how applications address the endpoint

Applications can connect through the Private Endpoint IP directly or through a private DNS hostname.

### Use `hostaddr` for Postgres without private DNS

Private DNS is optional for Postgres. Start with the connection string provided by the PlanetScale dashboard and add `hostaddr=<private-endpoint-ip>`. Keep the existing `host` value so PostgreSQL can use it for TLS hostname verification while connecting to the Private Endpoint IP.

For example:

```bash theme={null}
psql "host=<hostname-from-planetscale> hostaddr=$PRIVATE_ENDPOINT_IP port=5432 user=<username> password=<password> dbname=<database> sslnegotiation=direct sslmode=verify-full sslrootcert=system"
```

Use port `6432` instead when connecting through PgBouncer. PostgreSQL 17 or later is recommended when using `sslnegotiation=direct`.

### Configure private DNS

The public PlanetScale hostname continues to resolve to the public endpoint. Private Link uses a separate private DNS name supplied by PlanetScale, such as `<deployment>.private-pg.psdb.cloud`; it does not override the public `<deployment>.pg.psdb.cloud` zone.

Public connectivity is not automatically disabled when Private Link is enabled. We recommend confirming private connectivity first. Contact your Solutions Engineer to disable public connectivity.

Choose a single DNS record label for the Private Endpoint. The resulting private hostname is:

```text theme={null}
<record-name>.<private-dns-zone-from-planetscale>
```

Create the private zone, link it to your application VNet, and add an A record for the Private Endpoint IP:

```bash theme={null}
DNS_RESOURCE_GROUP="<dns-resource-group>"
PRIVATE_DNS_ZONE="<private-dns-zone-from-planetscale>"
DNS_RECORD_NAME="<record-name>"
VNET_ID=$(az network vnet show \
  --resource-group "$VNET_RESOURCE_GROUP" \
  --name "$VNET_NAME" \
  --query id \
  --output tsv)

az network private-dns zone create \
  --resource-group "$DNS_RESOURCE_GROUP" \
  --name "$PRIVATE_DNS_ZONE"

az network private-dns link vnet create \
  --resource-group "$DNS_RESOURCE_GROUP" \
  --zone-name "$PRIVATE_DNS_ZONE" \
  --name "${ENDPOINT_NAME}-vnet-link" \
  --virtual-network "$VNET_ID" \
  --registration-enabled false

az network private-dns record-set a add-record \
  --resource-group "$DNS_RESOURCE_GROUP" \
  --zone-name "$PRIVATE_DNS_ZONE" \
  --record-set-name "$DNS_RECORD_NAME" \
  --ipv4-address "$PRIVATE_ENDPOINT_IP"
```

If your applications use custom or on-premises DNS servers, configure those servers to resolve or forward the private zone to Azure DNS.

## Step 4: Verify connectivity

Run these checks from a host that uses the application VNet.

Confirm that the Private Endpoint accepts connections on the required database port. For direct PostgreSQL and PgBouncer connections:

```bash theme={null}
nc -vz "$PRIVATE_ENDPOINT_IP" 5432
nc -vz "$PRIVATE_ENDPOINT_IP" 6432
```

If you configured private DNS, confirm that the private hostname resolves to the Private Endpoint IP:

```bash theme={null}
dig +short <record-name>.<private-dns-zone-from-planetscale>
```

Then use either the dashboard hostname with `hostaddr=$PRIVATE_ENDPOINT_IP` or the private hostname in your connection string.

## Public and private access

> [!WARNING]
>
> Creating a Private Endpoint does not disable the deployment's public database endpoint. Private connectivity and public ingress are configured separately. After you verify private connectivity and update your applications, contact your PlanetScale Solutions Engineer if your deployment should accept private connections only.

## Troubleshooting

### The endpoint creation fails with `LinkedAuthorizationFailed`

For a cross-tenant connection, use the Private Link Service alias rather than its Azure resource ID. Set `--manual-request true` when using the Azure CLI.

### The endpoint remains pending

Confirm that PlanetScale configured the application subscription before you created the endpoint. If the subscription was added afterward, contact your Solutions Engineer. You may need to recreate the endpoint after PlanetScale confirms the configuration.

### The private hostname does not resolve to the Private Endpoint IP

The public `<deployment>.pg.psdb.cloud` hostname continues to resolve to the public endpoint while public ingress is enabled. If you ask PlanetScale to disable public ingress, PlanetScale removes the public DNS record. For private DNS, confirm that:

* You created the separate private DNS zone supplied by PlanetScale
* The A record points to the Private Endpoint's network-interface IP
* The private DNS zone is linked to the VNet used by your application
* Custom DNS servers forward or resolve the private zone correctly

This DNS configuration is not required when Postgres clients use both the dashboard `host` value and `hostaddr=<private-endpoint-ip>`.

### TLS hostname verification fails

When using `hostaddr`, keep the `host` value from the PlanetScale dashboard in the connection string. When using private DNS, use a single record label directly under the private DNS zone supplied by PlanetScale. Do not use the Private Endpoint IP or Private Link Service alias as the `host` value.

### Connections time out

Confirm that network security groups, user-defined routes, firewalls, and application egress policies allow the required database ports between the application and the Private Endpoint.

## Next steps

* [Connect to PlanetScale Postgres](../../../postgres/connecting.md)
* [Configure connection pooling with PgBouncer](../../../postgres/connecting/pgbouncer.md)
* [Learn about PlanetScale Managed on Azure](../azure.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
