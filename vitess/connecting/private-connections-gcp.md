---
url: https://planetscale.com/docs/vitess/connecting/private-connections-gcp
title: "Private Connections Gcp"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Connecting to PlanetScale privately via GCP Private Service Connect

When your compliance mandates that your connections do not route through the public Internet, PlanetScale provides private connection endpoints to GCP regions via [GCP Private Service Connect](https://cloud.google.com/vpc/docs/private-service-connect). GCP Private Service Connect is a form of *VPC peering* that keeps your traffic within Google Cloud. Private connections are included on the Base plan. There is no additional charge on PlanetScale’s end, but this may impact your GCP bill.

Below is a list of instructions to set up your VPC network to utilize a Private Service Connect endpoint when communicating with PlanetScale databases.

## Establishing a Private Service Connect Endpoint

## Verifying the connectivity of your Private Service Connect endpoint

GCP will automatically create a private Cloud DNS zone in the project where the PSC consumer endpoints are created.

The domain name depends on the region the consumer endpoint was created in. Refer to the table above. The format of the domain name will be:

- `<Endpoint-Name>.<Domain-Name>`

For example, if you chose `edge` as the endpoint name in the `us-central1` region, the domain name for the endpoint would be:

- `edge.gcp-us-central1.private-connect.psdb.cloud`

## Modifying your Connection Strings to utilize your Private Service Connect endpoint

By default, PlanetScale provides connection strings based on the `connect.psdb.cloud` domain name. To access your databases over the private endpoint change your connection string to match the `<Endpoint-Name>.<Domain-Name>` pattern.

For example, a connection string such as `gcp-us-central1.connect.psdb.cloud` would be changed to `edge.gcp-us-central1.private-connect.psdb.cloud` assuming `edge` was the Endpoint Name chosen during creation of the endpoint.

With this configured, you can leverage VPC peering to communicate between your GCP account and PlanetScale.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
