---
url: https://planetscale.com/docs/neki/connecting/private-connections/aws-privatelink
title: "Aws Privatelink"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Connect privately with AWS PrivateLink

> Private connectivity between an AWS VPC and Neki through AWS PrivateLink.

This page covers Neki.

Vitess version: [Vitess](https://planetscale.com/docs/vitess/connecting/private-connections.md)

Postgres version: [Postgres](https://planetscale.com/docs/postgres/connecting/private-connections/aws-privatelink.md)

[AWS PrivateLink](https://aws.amazon.com/privatelink/) keeps traffic between
your VPC and Neki on the AWS network without traversing the public internet.
Use it when your compliance requirements or network architecture require
private database connectivity.

Standard Neki connections use TLS over the public internet and are appropriate
for most applications. PrivateLink adds network isolation; Neki still requires
TLS for connections through the private endpoint.

## Pricing

PlanetScale bills PrivateLink ingress and egress at **\$0.01 per GB** after the
branch's included allowance. AWS also charges for VPC endpoints and data
processing. See [Neki pricing](https://planetscale.com/docs/neki/pricing.md#private-traffic) and the
[AWS PrivateLink pricing page](https://aws.amazon.com/privatelink/pricing/).

## Prerequisites

* A Neki database in an AWS region.
* An AWS VPC in the same region as the Neki database.
* IAM permissions to create VPC endpoints and manage their security groups.
* A host in the VPC from which you can test DNS and Postgres connectivity.

## Get the private connection details

**Step 1**

From the PlanetScale dashboard, select your Neki database.

**Step 2**

Open **Settings**, then select **Roles**.

**Step 3**

Select the branch and open the role your application uses.

**Step 4**

Select the required router group under **Connection target**.

**Step 5**

Copy the **Private Host** and **Private Service Name**.

The private host and service name identify the Neki service. The role's
generated username still identifies the branch and selected router group. Copy
the complete username from the dashboard instead of constructing it.

Use the service name shown for each role and connection target. Do not assume
that every Neki service in an AWS region has the same service name. An existing
VPC endpoint can be reused only when the dashboard shows the same **Private
Service Name** for the new connection.

## Create the VPC endpoint

**Step 1**

Create a security group for the endpoint that allows inbound TCP traffic on
port `5432` from your application resources. Referencing the applications'
security group is more restrictive than allowing the entire VPC CIDR.

**Step 2**

In the AWS console, open **VPC** > **Endpoints** and select **Create
endpoint**.

**Step 3**

For the endpoint type, select **Endpoint services that use NLBs and GWLBs**.

**Step 4**

Enter the **Private Service Name** from PlanetScale and select **Verify
service**.

**Step 5**

Select the VPC and the subnets your application can reach. Select at least
two subnets for availability.

**Step 6**

Under **Additional settings**, enable the DNS name.

**Step 7**

Attach the endpoint security group and create the endpoint.

**Step 8**

Wait until the endpoint status is **Available**.

Your application resources must also allow outbound TCP traffic on port
`5432`. If the selected subnets use restrictive network ACLs, allow port
`5432` and the ephemeral ports required for response traffic.

## Verify the endpoint

From a host in the configured VPC, confirm that the **Private Host** resolves
to private addresses:

```bash theme={null}
dig +short <PRIVATE_HOST>
```

Then connect using the private host and the username and password for your
role:

```bash theme={null}
psql 'host=<PRIVATE_HOST> port=5432 user=<USERNAME> password=<PASSWORD> dbname=postgres sslnegotiation=direct sslmode=verify-full sslrootcert=system'
```

Update application connection strings by replacing the public host with the
private host. Keep the generated username, port, database, TLS settings, and
password unchanged.

## Troubleshooting

If the connection fails:

1. Confirm that the VPC endpoint status is **Available**.
2. Confirm that the endpoint is in the same AWS region as the Neki database.
3. Check inbound rules on the endpoint security group and outbound rules on
   the application security group.
4. Check the network ACLs for the endpoint and application subnets.
5. Verify that the private host resolves to addresses in the VPC.
6. Use [AWS Reachability Analyzer](https://docs.aws.amazon.com/vpc/latest/reachability/what-is-reachability-analyzer.html)
   to inspect the path between the application and endpoint.

## Related documentation

* [Connect to Neki](https://planetscale.com/docs/neki/connecting.md)
* [Roles and credentials](https://planetscale.com/docs/neki/connecting/roles.md)
* [Neki pricing](https://planetscale.com/docs/neki/pricing.md#private-traffic)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
