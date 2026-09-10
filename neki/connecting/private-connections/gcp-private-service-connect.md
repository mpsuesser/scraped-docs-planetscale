---
url: https://planetscale.com/docs/neki/connecting/private-connections/gcp-private-service-connect
title: "Gcp Private Service Connect"
description: ""
access_date: 2026-09-10T14:51:28.297Z
current_date: 2026-09-10T14:51:28.297Z
---

[GCP Private Service Connect](https://cloud.google.com/vpc/docs/private-service-connect) keeps traffic between your VPC and Neki on the Google Cloud network without traversing the public internet. Use it when your compliance requirements or network architecture require private database connectivity.

Standard Neki connections use TLS over the public internet and are appropriate for most applications. Private Service Connect adds network isolation; Neki still requires TLS for connections through the private endpoint.

## Pricing

PlanetScale bills Private Service Connect ingress and egress at **$0.01 per GB** after the branch’s included allowance. Google Cloud also charges for Private Service Connect endpoints and applicable network transfer. See [Neki pricing](../../pricing.md#private-traffic) and the [Google Cloud VPC pricing page](https://cloud.google.com/vpc/pricing#private-service-connect).

## Prerequisites

- A Neki database in a GCP region.
- A Google Cloud VPC in the same region as the Neki database.
- IAM permissions to create Private Service Connect endpoints.
- The Cloud DNS and Service Directory APIs enabled in the Google Cloud project.
- A host in the VPC from which you can test DNS and Postgres connectivity.

## Get the private connection details

The private host and service name identify the Neki service. The role’s generated username still identifies the branch and selected router group. Copy the complete username from the dashboard instead of constructing it.

Use the service name shown for each role and connection target. Do not assume that every Neki service in a Google Cloud region has the same service name. An existing endpoint can be reused only when the dashboard shows the same **Private Service Name** for the new connection.

## Create the Private Service Connect endpoint

## Build and verify the private hostname

The hostname combines the endpoint name from Google Cloud with the **Private Host** from PlanetScale:

```text
<ENDPOINT_NAME>.<PRIVATE_HOST>
```

For example, an endpoint named `planetscale-main` and a private host of `gcp-us-central1-1.private-pg.psdb.cloud` produce:

```text
planetscale-main.gcp-us-central1-1.private-pg.psdb.cloud
```

From a host in the configured VPC, confirm that the hostname resolves to the endpoint’s private address:

```shellscript
dig +short <ENDPOINT_NAME>.<PRIVATE_HOST>
```

Then connect using the private hostname and the username and password for your role:

```shellscript
psql 'host=<ENDPOINT_NAME>.<PRIVATE_HOST> port=5432 user=<USERNAME> password=<PASSWORD> dbname=postgres sslnegotiation=direct sslmode=verify-full sslrootcert=system'
```

Update application connection strings by replacing the public host with the combined private hostname. Keep the generated username, port, database, TLS settings, and password unchanged.

## Troubleshooting

If the connection fails:

1. Confirm that the endpoint status is **Accepted**.
2. Confirm that the endpoint is in the same GCP region as the Neki database.
3. Verify that the Cloud DNS and Service Directory APIs are enabled.
4. Check that the application can route to the endpoint subnet.
5. Confirm that the combined hostname resolves to the endpoint’s private address.
6. Check firewall rules for outbound TCP traffic on port `5432`.

## Related documentation

- [Roles and credentials](../roles.md)
- [Neki pricing](../../pricing.md#private-traffic)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
