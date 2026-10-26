---
url: https://planetscale.com/docs/vitess/connecting/private-connections
title: "Private Connections"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Connecting to PlanetScale privately on AWS

This page covers Vitess.

Postgres version: [Postgres](https://planetscale.com/docs/postgres/connecting/private-connections/aws-privatelink.md)

Neki version: [Neki](https://planetscale.com/docs/neki/connecting/private-connections/aws-privatelink.md)

## Connecting to PlanetScale privately via AWS PrivateLink

When your compliance mandates that your connections do not route through the public Internet, PlanetScale provides private connection endpoints to AWS regions via [AWS PrivateLink](https://aws.amazon.com/privatelink/). AWS PrivateLink is a form of *VPC peering* that does not send your traffic over the public internet. Private connections are included on the Base plan. There is no additional charge on PlanetScale's end, but this may impact your AWS bill.

Below is a list of instructions to set up your Virtual Private Cloud (VPC) to utilize a VPC endpoint when communicating with PlanetScale databases.

## Establishing a VPC endpoint

**Step 1**

Identify the AWS region that your VPC lives in, which we will refer to as `<aws-region>` for the rest of this document.

**Step 2**

Navigate to the "Endpoints" section on the VPC page and select "**Create Endpoint**."

![Create a new endpoint](https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/images/private-connections/create-new-endpoint.png?fit=max\&auto=format\&n=g0AZZQkXmTSBYuKj\&q=85\&s=cf911b5cbce910b31409f9095ef45cff)

**Step 3**

Select "Endpoint services that use NLBs and GWLBs"

![Menu to select endpoint type](https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/images/private-connections/type-of-endpoint.png?fit=max\&auto=format\&n=g0AZZQkXmTSBYuKj\&q=85\&s=d13fb4258236fdea6b103ef1aed9fb59)

| AWS Region Name | AWS Region | VPC Endpoint Service Name |
| :- | :- | :- |
| US East (Ohio) | us-east-2 | `com.amazonaws.vpce.us-east-2.vpce-svc-069f88c102c1a7fba` |
| US East (N. Virginia) | us-east-1 | `com.amazonaws.vpce.us-east-1.vpce-svc-02fef31be60d3fd35` |
| US West (Oregon) | us-west-2 | `com.amazonaws.vpce.us-west-2.vpce-svc-0f63a383cb2d41919` |
| Asia Pacific (Mumbai) | ap-south-1 | `com.amazonaws.vpce.ap-south-1.vpce-svc-06556ed2371c5fdd2` |
| Asia Pacific (Singapore) | ap-southeast-1 | `com.amazonaws.vpce.ap-southeast-1.vpce-svc-046d8feae38660302` |
| Asia Pacific (Sydney) | ap-southeast-2 | `com.amazonaws.vpce.ap-southeast-2.vpce-svc-03e5578eeaf446c90` |
| Asia Pacific (Tokyo) | ap-northeast-1 | `com.amazonaws.vpce.ap-northeast-1.vpce-svc-099c246fa320e54d1` |
| Europe (Frankfurt) | eu-central-1 | `com.amazonaws.vpce.eu-central-1.vpce-svc-091260498e58d4dc3` |
| Europe (Ireland) | eu-west-1 | `com.amazonaws.vpce.eu-west-1.vpce-svc-049577caa775e8648` |
| Europe (London) | eu-west-2 | `com.amazonaws.vpce.eu-west-2.vpce-svc-0f69e183c9a555f03` |
| South America (São Paulo) | sa-east-1 | `com.amazonaws.vpce.sa-east-1.vpce-svc-09b11604d399b5c58` |
| Canada (Montreal) | ca-central-1 | `com.amazonaws.vpce.ca-central-1.vpce-svc-0617a00ea4e327520` |

**Step 4**

Fill the "Service name" text box according to which region you want to establish AWS PrivateLink for. Once you have filled in the Service Name text box, click "Verify service".

![Endpoint service name and verification](https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/images/private-connections/service-name-and-verification.png?fit=max\&auto=format\&n=g0AZZQkXmTSBYuKj\&q=85\&s=078bc7b8eb16e49acfec3babb8a0014d)

**Step 5**

Choose the VPC and subnets that should be peered with the PlanetScale service endpoint.

**Step 6**

Click the "Additional settings" dropdown arrow to reveal the "DNS name" configuration, and select the "Enable DNS name" checkbox.

**Step 7**

Choose the security group of your choice to control what can send traffic to the PlanetScale service endpoint.

**Step 8**

Click "Create endpoint" and verify that the VPC endpoint's status reports "Available" after a few minutes.

## Verifying the connectivity of your VPC endpoint

**Step 1**

In the AWS UI, confirm that the endpoint has successfully been created by verifying that the Status section of the endpoint reads "Available".

> [!WARNING]
>
> Some PlanetScale regions are named differently than AWS Provider regions. We will refer to the PlanetScale region as `<planetscale-region>` for the rest of this document.

**Step 2**

Confirm that the Private DNS Names reads: `<planetscale-region>.private-connect.psdb.cloud`.

**Step 3**

Log into any EC2 instance in the configured VPC and run `dig +short <planetscale-region>.private-connect.psdb.cloud` to confirm that DNS resolution is producing IP Addresses in the range of your VPC's CIDR.

```bash theme={null}
dig +short us-east.private-connect.psdb.cloud
172.31.16.197
172.31.13.7
```

**Step 4**

Run `curl https://<planetscale-region>.private-connect.psdb.cloud` to verify your connectivity. A successful response will yield `Welcome to PlanetScale`.

```
curl https://us-east.private-connect.psdb.cloud
Welcome to PlanetScale.
```

## Modifying your Connection Strings to utilize your VPC endpoint.

By default, PlanetScale provides users with a connection string that reads `<planetscale-region>.connect.psdb.cloud`.

To utilize your newly configured VPC endpoint, prepend `private-` to the `connect` subdomain as shown above, yielding a connection string that reads `<planetscale-region>.private-connect.psdb.cloud`.

With this configured, you can leverage VPC peering to communicate between your AWS account and PlanetScale.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
