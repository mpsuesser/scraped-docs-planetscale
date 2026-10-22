---
url: https://planetscale.com/docs/plans/managed/aws/vpc-peering
title: "Vpc Peering"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Set up AWS VPC peering with PlanetScale Managed

> Private database connectivity between your VPC and a PlanetScale Managed deployment over AWS VPC peering.

## Overview

[AWS VPC peering](https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html) establishes private IP connectivity between your application VPC and the VPC containing your PlanetScale Managed deployment. Traffic uses the AWS network instead of traversing the public internet.

PlanetScale will send a peering request to your AWS account, configure routes and database access in the Managed VPC, and authorize your VPC to use a Route 53 private hosted zone. In your AWS console you will need to accept the request and configure routes and network access in your VPC, and associate your VPC with the private hosted zone.

> [!NOTE]
>
> VPC peering is available for PlanetScale Managed deployments on AWS. Contact your PlanetScale Solutions Engineer to confirm that it is appropriate for your network architecture. Peering does not disable the public database endpoint; clients must use the dedicated private hostname to connect over the peering connection.

## Prerequisites

Before requesting a peering connection:

* Install and configure the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html). The AWS CLI is required to associate your VPC with the private hosted zone.
* Confirm that you own the VPC. Participants in a shared VPC cannot accept peering requests.
* Your VPC's IPv4 CIDR blocks must not overlap the PlanetScale Managed VPC's IPv4 CIDR blocks. PlanetScale provides the Managed VPC CIDR blocks.
* Enable both `enableDnsSupport` and `enableDnsHostnames` on your VPC. In the [Amazon VPC console](https://console.aws.amazon.com/vpc/), select **Your VPCs**, select your VPC, then select **Actions** and **Edit VPC settings**. Enable both **DNS resolution** and **DNS hostnames**, then save your changes. For AWS CLI instructions, see [View and update DNS attributes for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns-updating.html).
* Identify every route table used by clients that connect to PlanetScale.
* Review security groups and network ACLs for database traffic:
  * Vitess uses TCP ports `443` and `3306`.
  * Postgres uses TCP ports `443`, `5432`, and `6432`.

VPC peering is not transitive. Clients must reside in the directly peered VPC; they cannot reach the Managed VPC through a transit gateway, VPN, Direct Connect connection, NAT device, or another peered VPC.

## Step 1: Initiate the setup

Contact your PlanetScale Solutions Engineer and provide:

* Your AWS account ID
* The AWS Region of your VPC
* Your VPC ID
* Every IPv4 CIDR block associated with your VPC

PlanetScale verifies that the CIDR blocks do not overlap, creates the peering request, and configures the PlanetScale side of the connection. Your Solutions Engineer then provides:

* The VPC peering connection ID, which starts with `pcx-`
* The AWS account ID and VPC ID of the PlanetScale side of the connection
* The PlanetScale Managed VPC CIDR blocks
* The Route 53 private hosted zone ID
* The private hosted zone name
* Your dedicated private database hostname

> [!WARNING]
>
> Accept only the peering connection ID supplied by PlanetScale. An unexpected peering request can grant an unknown AWS account network access to your VPC.

## Step 2: Accept the VPC peering request

The peering request remains in `pending-acceptance` for seven days. Accept it in the Region containing your VPC.

**AWS Console**

**Step 1**

Open the [Amazon VPC console](https://console.aws.amazon.com/vpc/) and select the Region containing your VPC.

**Step 2**

In the navigation pane, select **Peering connections**.

**Step 3**

Select the peering connection ID supplied by PlanetScale. Confirm that its status is **Pending acceptance**, the requester account and VPC match the information from PlanetScale, and the accepter account and VPC are yours.

**Step 4**

Select **Actions**, then **Accept request**. Confirm the request.

**Step 5**

Wait for the connection status to become **Active**.

**AWS CLI**

Inspect the request before accepting it:

```shell theme={null}
aws ec2 describe-vpc-peering-connections \
  --region <AWS_REGION> \
  --vpc-peering-connection-ids <VPC_PEERING_CONNECTION_ID> \
  --query 'VpcPeeringConnections[0].{Status:Status.Code,RequesterAccount:RequesterVpcInfo.OwnerId,RequesterVpc:RequesterVpcInfo.VpcId,AccepterAccount:AccepterVpcInfo.OwnerId,AccepterVpc:AccepterVpcInfo.VpcId}' \
  --output table
```

Confirm that the status is `pending-acceptance`, the requester account and VPC match the information from PlanetScale, and the accepter account and VPC are yours.

Accept the request:

```shell theme={null}
aws ec2 accept-vpc-peering-connection \
  --region <AWS_REGION> \
  --vpc-peering-connection-id <VPC_PEERING_CONNECTION_ID>
```

Check the status again:

```shell theme={null}
aws ec2 describe-vpc-peering-connections \
  --region <AWS_REGION> \
  --vpc-peering-connection-ids <VPC_PEERING_CONNECTION_ID> \
  --query 'VpcPeeringConnections[0].Status.Code' \
  --output text
```

Continue when the command returns:

```text theme={null}
active
```

## Step 3: Configure routes and network access

Add a route to each route table associated with clients that connect to PlanetScale. The destination is a PlanetScale Managed VPC CIDR block and the target is the VPC peering connection.

**AWS Console**

**Step 1**

In the Amazon VPC console, select **Route tables**.

**Step 2**

Select a route table associated with a client subnet, then select **Actions** and **Edit routes**.

**Step 3**

Add a route with a PlanetScale Managed VPC CIDR block as the **Destination** and the peering connection as the **Target**.

**Step 4**

Save the route and repeat these steps for every Managed VPC CIDR block and client route table supplied by PlanetScale.

**AWS CLI**

Run this command for every combination of client route table and Managed VPC CIDR block:

```shell theme={null}
aws ec2 create-route \
  --region <AWS_REGION> \
  --route-table-id <ROUTE_TABLE_ID> \
  --destination-cidr-block <MANAGED_VPC_CIDR> \
  --vpc-peering-connection-id <VPC_PEERING_CONNECTION_ID>
```

Verify the routes:

```shell theme={null}
aws ec2 describe-route-tables \
  --region <AWS_REGION> \
  --route-table-ids <ROUTE_TABLE_ID> \
  --query 'RouteTables[0].Routes[?VpcPeeringConnectionId!=`null`].[DestinationCidrBlock,VpcPeeringConnectionId,State]' \
  --output table
```

Each route should reference the supplied peering connection ID and have an `active` state.

If your client security groups restrict outbound traffic, allow TCP traffic to the Managed VPC CIDR blocks on the required ports. If your network ACLs restrict traffic, allow the database connection and return traffic.

PlanetScale configures the return routes and database access controls in the Managed VPC. Confirm with your Solutions Engineer that the PlanetScale route tables contain return routes for every client CIDR block you supplied.

## Step 4: Associate the private hosted zone

VPC peering does not automatically change how the dedicated database hostname resolves. PlanetScale creates a Route 53 private hosted zone containing the private database records and authorizes your VPC to use it. You must complete the cross-account association.

> [!WARNING]
>
> AWS does not support cross-account private hosted zone associations in the Route 53 console. Use the AWS CLI, an AWS SDK, or the Route 53 API.

After your Solutions Engineer confirms that PlanetScale created the association authorization, run:

```shell theme={null}
aws route53 associate-vpc-with-hosted-zone \
  --hosted-zone-id <PRIVATE_HOSTED_ZONE_ID> \
  --vpc VPCRegion=<AWS_REGION>,VPCId=<VPC_ID>
```

The command returns a change ID. Check the change status:

```shell theme={null}
aws route53 get-change \
  --id <CHANGE_ID> \
  --query 'ChangeInfo.Status' \
  --output text
```

Continue when the command returns `INSYNC`.

Verify the association:

```shell theme={null}
aws route53 list-hosted-zones-by-vpc \
  --vpc-id <VPC_ID> \
  --vpc-region <AWS_REGION> \
  --query 'HostedZoneSummaries[].{Id:HostedZoneId,Name:Name,Owner:Owner}'
```

Confirm that the output contains the private hosted zone ID and narrowly scoped zone name supplied by PlanetScale. PlanetScale can remove the unused association authorization after the association succeeds without affecting DNS.

## Step 5: Verify DNS and database connectivity

Run DNS and connection tests from a resource in a subnet that uses the configured route table.

Use the dedicated private database hostname supplied by your Solutions Engineer:

* Vitess hostnames end in `.private-connect.psdb.cloud`.
* Postgres hostnames end in `.private-pg.psdb.cloud`.

First, resolve the hostname:

```shell theme={null}
dig +short <PRIVATE_DATABASE_HOSTNAME>
```

The hostname should resolve to private IP addresses within the PlanetScale Managed VPC CIDR blocks. If it resolves to a public IP address, traffic will not use the peering connection. Verify the private hosted zone association and the DNS settings on your VPC.

> [!NOTE]
>
> AmazonProvidedDNS resolves the private hosted zone automatically. If your clients use a custom DNS resolver, ask your infrastructure team to configure conditional forwarding for the private hosted zone through a [Route 53 Resolver inbound endpoint](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-forwarding-inbound-queries.html) in your VPC.

Next, create a connection string using the **Connect** button in the PlanetScale dashboard or the [pscale CLI](https://planetscale.com/docs/cli.md), then connect with the appropriate database client:

**MySQL**

```shell theme={null}
mysql -h <PRIVATE_DATABASE_HOSTNAME> -u <USERNAME> -p \
  --ssl-mode=VERIFY_IDENTITY \
  --ssl-ca=/etc/pki/tls/certs/ca-bundle.crt
```

A successful connection displays the `mysql>` prompt.

> [!NOTE]
>
> The CA root path depends on your operating system. See the [CA root configuration documentation](https://planetscale.com/docs/vitess/connecting/secure-connections.md#ca-root-configuration) for the correct path.

**Postgres**

```shell theme={null}
psql -W 'host=<PRIVATE_DATABASE_HOSTNAME> port=5432 user=<USERNAME> dbname=<DATABASE> sslnegotiation=direct sslmode=verify-full sslrootcert=system'
```

A successful connection displays the `postgres=>` prompt.

> [!NOTE]
>
> PostgreSQL 17 or later is required for `sslnegotiation=direct`. If `sslrootcert=system` does not work in your environment, use the path to your system root CA certificate. See the [Postgres connection documentation](https://planetscale.com/docs/postgres/connecting/quickstart.md#secure-connections).

If DNS resolves to a private address but the client cannot connect:

* Confirm that the peering connection and routes are `active`.
* Confirm that the route tables associated with the client subnets contain every Managed VPC CIDR block.
* Confirm that security groups and network ACLs allow the database connection and return traffic.
* Ask your Solutions Engineer to verify the return routes and access controls in the Managed VPC.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
