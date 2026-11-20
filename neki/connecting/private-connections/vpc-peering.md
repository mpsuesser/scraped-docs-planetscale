---
url: https://planetscale.com/docs/neki/connecting/private-connections/vpc-peering
title: "Vpc Peering"
description: ""
access_date: 2026-11-20T13:37:54.137Z
current_date: 2026-11-20T13:37:54.137Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Connect directly to Neki routers over VPC peering

> Private connectivity from a peered cloud network straight to the routers of a Neki database.

VPC peering gives your cloud network the shortest path to your Neki database. Clients connect straight to the routers with no load balancer in between. This gives you:

* **Lower latency.** Each query skips the network hop through the PlanetScale load balancer.
* **Lower cross-zone costs.** Per-zone hostnames keep each client's connections inside its own zone. That avoids cross-zone latency and data transfer charges.
* **Private traffic.** Traffic stays off the public internet.

PlanetScale peers its network with yours and configures its side of the connection. You accept the peering in your cloud account. You then configure routes and network access and set up DNS for the private hostnames.

> [!NOTE]
>
> VPC peering is available for single-tenant and PlanetScale Managed Neki deployments in AWS and GCP. It is not available in multi-tenant regions. PlanetScale must enable VPC peering for your deployment before you can use it. Contact your PlanetScale Solutions Engineer to enable it. Peering does not disable the public endpoint. Clients must use the private hostnames to connect over the peering connection.

## Set up VPC peering

The setup steps depend on your cloud provider:

* [AWS VPC peering](https://planetscale.com/docs/plans/managed/aws/vpc-peering.md)

For GCP contact your PlanetScale Solutions Engineer.

## Compare with private endpoints

| | VPC peering | Private endpoint ([AWS PrivateLink](https://planetscale.com/docs/neki/connecting/private-connections/aws-privatelink.md) or [GCP Private Service Connect](https://planetscale.com/docs/neki/connecting/private-connections/gcp-private-service-connect.md)) |
| - | - | - |
| Path | Your network to the Neki routers | Your endpoint to a PlanetScale load balancer to the Neki routers |
| Supported on | Single-tenant and Managed deployments in AWS and GCP | Neki databases in AWS and GCP |
| CIDR blocks | Must not overlap the PlanetScale network | Can overlap |
| Zone-local connections | Yes (per-zone hostname without automatic failover) | No |

## Hostnames

Each router group gets one hostname for all of its zones and one hostname per zone:

| Covers | Hostname |
| - | - |
| Every zone of the router group | `<ROUTER_GROUP>-<BRANCH_ID>.<DNS_SUFFIX>` |
| One zone | `<ROUTER_GROUP>-<ZONE_ID>-<BRANCH_ID>.<DNS_SUFFIX>` |

* `<ROUTER_GROUP>` is the router group name such as `default`.
* `<BRANCH_ID>` is the branch ID.
* `<ZONE_ID>` is the zone ID on AWS such as `use1-az1`. It is the zone name on GCP such as `us-central1-a`. AWS maps zone names to different physical zones in each account, so Neki uses the zone ID there.
* `<DNS_SUFFIX>` ends in `.direct.psdb.cloud`. PlanetScale supplies it.

For example:

```text theme={null}
default-r8t2w5n0b4hc.7y26zavxiwv6-useast1-1.direct.psdb.cloud
default-use1-az1-r8t2w5n0b4hc.7y26zavxiwv6-useast1-1.direct.psdb.cloud
```

The router group hostname resolves to the routers in every zone. A per-zone hostname resolves only to the routers in that zone. Use it to keep connections inside the client's zone and avoid cross-zone latency and data transfer charges.

> [!WARNING]
>
> A per-zone hostname has no automatic failover. If that zone is impaired or unavailable then connections to its hostname fail or time out. Neki does not move a per-zone hostname to another zone. Your application must handle the fallback itself.

When you use per-zone hostnames give your client a fallback order. For example a client in `use1-az1` can:

1. Connect to the `use1-az1` hostname.
2. If that fails, connect to the hostname of another zone such as `use1-az2`.
3. If every per-zone hostname fails, connect to the router group hostname.

Return to the client's own zone when it recovers. Otherwise the client keeps paying for cross-zone traffic after the zone recovers.

### Connection best practices

The hostnames point at individual routers and not at a load balancer. Neki updates the records when routers drain, restart or move, which happens frequently, especially with router auto-scaling enabled. The records have a short TTL.

Follow these best practices in your client:

* Resolve the hostname for every new connection and respect the DNS TTL.
* Connect with the hostname. Do not put router IP addresses in configuration or connection strings.
* Spread connections across every address the hostname returns. libpq 16 and later does this with `load_balance_hosts=random`.
* Set a maximum connection lifetime in your connection pool such as 5 to 15 minutes.

> [!WARNING]
>
> A client that does not follow these best practices can:
>
> * Connect to a router that no longer serves it.
> * Send all of its connections to one router while other routers sit idle.
> * Keep long-lived connections on old routers and never use new ones.

> [!NOTE]
>
> The JVM caches DNS results for 30 seconds by default. Set `networkaddress.cache.ttl=1` and `networkaddress.cache.negative.ttl=3` in the `java.security` file or with `java.security.Security.setProperty` before the application does its first lookup.

## Connect

The PlanetScale dashboard shows the host and username for VPC peering connections.

**Step 1**

From the PlanetScale dashboard, select your Neki database.

**Step 2**

Open **Settings**, then select **Roles**.

**Step 3**

Open the role your application uses.

**Step 4**

Under **Connection details** check that the **Connection method** is **VPC peering**.

**Step 5**

Select the **Router** and the **Query target**.

**Step 6**

Copy the **Host** and **Username**. To connect to one zone expand **Availability zone hostnames** and copy the host for that zone.

**VPC peering** shows a link to this page instead of connection details until VPC peering is set up for the branch. Use the **Load balancer** connection method in the meantime.

The **Username** for VPC peering differs from the load balancer username. It has no branch suffix because the host already identifies the branch and router group. Use the username that matches the connection method.

Then connect with the host, username and password:

```shell theme={null}
psql 'host=<HOSTNAME> port=5432 user=<USERNAME> password=<PASSWORD> dbname=postgres sslnegotiation=direct sslmode=verify-full sslrootcert=system'
```

Neki accepts VPC peering connections only on port `5432`. It requires TLS and refuses plaintext. The routers present a certificate for `*.<DNS_SUFFIX>`.

> [!NOTE]
>
> `sslnegotiation=direct` requires PostgreSQL 17 or later. Older clients can omit it and still use TLS.

## Troubleshooting

If the hostname does not resolve or the connection fails, check the steps for your cloud provider first. Then:

1. Add `sslmode=verify-full` if Neki refuses the connection for not using TLS.
2. Check that you use the **VPC peering** username from the dashboard and not the load balancer username.
3. Ask your Solutions Engineer to check the return routes and access controls on the PlanetScale side.

## Related documentation

* [Connect to Neki](https://planetscale.com/docs/neki/connecting.md)
* [Roles and credentials](https://planetscale.com/docs/neki/connecting/roles.md)
* [AWS PrivateLink](https://planetscale.com/docs/neki/connecting/private-connections/aws-privatelink.md)
* [GCP Private Service Connect](https://planetscale.com/docs/neki/connecting/private-connections/gcp-private-service-connect.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
