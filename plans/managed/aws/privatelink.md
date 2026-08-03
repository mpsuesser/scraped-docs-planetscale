---
url: https://planetscale.com/docs/plans/managed/aws/privatelink
title: "Privatelink"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

## Overview

If you are on the Base plan and would like to set up AWS PrivateLink, see our [Private connections documentation](../../../vitess/connecting/private-connections.md).

## How PlanetScale Managed and AWS PrivateLink work

AWS PrivateLink requires two components:

- A [VPC endpoint service](https://docs.aws.amazon.com/vpc/latest/privatelink/endpoint-service-overview.html) deployed in the AWS Organizations member account that PlanetScale controls.
- A [VPC endpoint interface](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html), sometimes referred to as a “VPC endpoint” in AWS, deployed in the account that your applications operate in.

Once both components are operating correctly, the EC2 instances in the VPC that the VPC endpoint has been assigned to will leverage [Private DNS](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#vpce-private-dns) to connect to your VPC endpoint instead of the publicly accessible endpoint.

The connection strings that PlanetScale provides will operate successfully inside and outside your VPC, creating PrivateLink connections inside of your VPC and regular connections outside of your VPC.

## Step 1: Initiating the setup process

There is no fully automated way to establish a PrivateLink connection. If you would like to initiate the process, please get in touch with your Solutions Engineer and let them know the [AWS Account ID](https://docs.aws.amazon.com/IAM/latest/UserGuide/console_account-alias.html) that you intend to create the [VPC endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints.html) in.

Once they receive your AWS Account ID and forward it to the team responsible for provisioning your deployment, the team will provide the Solutions Engineer (and ultimately you) with the Service Name of the [VPC endpoint service](https://docs.aws.amazon.com/vpc/latest/privatelink/endpoint-service-overview.html) that will be responsible for accepting your connection.

It is important to keep the service name in your records. It is the only piece of information you need to input when creating your VPC endpoint.

## Step 2: Establishing a VPC endpoint connection

Only proceed to the next steps once a PlanetScale Solutions Engineer has provided the service name and confirmed cross-account authentication has been configured.

The following steps are an example of establishing a VPC endpoint connection in the AWS Console. In this example, the customer has requested that their deployment be in the `eu-west-1` region.

When you go through the steps, make sure that you have selected the region that matches the region that your PlanetScale Managed cluster deployment has been provisioned into.

## Step 3: Verifying a VPC endpoint connection

PlanetScale publishes a [wildcard DNS record](https://en.wikipedia.org/wiki/Wildcard_DNS_record) for your private region. AWS PrivateLink will override the DNS record in your VPC to point to your VPC endpoint instead of the publicly published record.

To verify that the DNS override is working correctly, issue the following `dig` command using the value of your “Private DNS Name” instead of the value in the example:

```shellscript
dig +short wildcard.frzzbztuqm3h-euwest1-1.psdb.cloud
172.31.16.197
172.31.13.7
```

If your `dig` command returns a set of static IP addresses, your VPC Endpoint connection is operating successfully. If it returns a `CNAME` to an ELB record (for example, something like `something.elb.region.amazoneaws.com`), your connection is not operating successfully, and you should consult your Solutions Engineer.

Once you’ve verified that your connection is operating successfully, you will need to verify that you can reach a database you’ve provisioned:

If your client connects successfully, you have confirmed that your connections to PlanetScale will be established through AWS PrivateLink. If you cannot connect, please consult your Solutions Engineer.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
