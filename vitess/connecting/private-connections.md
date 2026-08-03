---
url: https://planetscale.com/docs/vitess/connecting/private-connections
title: "Private Connections"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

## Connecting to PlanetScale privately via AWS PrivateLink

When your compliance mandates that your connections do not route through the public Internet, PlanetScale provides private connection endpoints to AWS regions via [AWS PrivateLink](https://aws.amazon.com/privatelink/). AWS PrivateLink is a form of *VPC peering* that does not send your traffic over the public internet. Private connections are included on the Base plan. There is no additional charge on PlanetScale’s end, but this may impact your AWS bill.

Below is a list of instructions to set up your Virtual Private Cloud (VPC) to utilize a VPC endpoint when communicating with PlanetScale databases.

## Establishing a VPC endpoint

## Verifying the connectivity of your VPC endpoint

## Modifying your Connection Strings to utilize your VPC endpoint.

By default, PlanetScale provides users with a connection string that reads `<planetscale-region>.connect.psdb.cloud`.

To utilize your newly configured VPC endpoint, prepend `private-` to the `connect` subdomain as shown above, yielding a connection string that reads `<planetscale-region>.private-connect.psdb.cloud`.

With this configured, you can leverage VPC peering to communicate between your AWS account and PlanetScale.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
