---
url: https://planetscale.com/docs/postgres/connecting/private-connections/gcp-private-service-connect
title: "Gcp Private Service Connect"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Connect privately with GCP Private Service Connect

> When you use GCP Private Service Connect, your network traffic between your VPC and PlanetScale stays within the Google Cloud network, without traversing the public internet.

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

<PlatformAvailability current="postgres" vitess="/vitess/connecting/private-connections-gcp" />

[GCP Private Service Connect](https://cloud.google.com/vpc/docs/private-service-connect) is a highly available, scalable technology that enables you to privately connect your VPC to supported GCP services, endpoint services, and partner services.

### When to use GCP Private Service Connect

By default, PlanetScale Postgres databases use secure connections over the public internet with industry-standard TLS encryption. This approach is secure and meets the needs of most customers. However, you may want to consider GCP Private Service Connect if:

* **Compliance requirements**: Your organization has regulatory or compliance mandates that require database connections to avoid the public internet entirely
* **Enhanced security posture**: You want an additional layer of network isolation for sensitive data workloads
* **Network architecture**: Your existing GCP infrastructure is designed around private connectivity patterns
* **Reduced network latency**: GCP Private Service Connect can help reduce latency by keeping traffic within Google's network backbone

GCP Private Service Connect provides these security and compliance benefits by ensuring your database traffic never leaves the Google Cloud network.

<Note>
  Normal PlanetScale Postgres connectivity (as described in our [standard connection documentation](../../connecting.md)) uses secure TLS encryption over the public internet and is appropriate for most use cases. GCP Private Service Connect is primarily beneficial for compliance and enhanced security requirements.
</Note>

### Private Service Connect pricing

PlanetScale charges a flat rate of **\$0.01/GB** for all network traffic (both egress and ingress) over private connections.
This replaces the standard [egress pricing](../../pricing.md#network-data-transfer) that applies to public connections.
Each branch has a default amount of included private network traffic before this rate takes effect.

Google Cloud also charges standard Private Service Connect pricing for endpoints, which includes:

* **Private Service Connect endpoint charges**: Based on your endpoint configuration and usage
* **Network egress charges**: Standard GCP egress pricing may apply for data transfer

For current GCP pricing in your region, see the [GCP Private Service Connect pricing page](https://cloud.google.com/vpc/pricing#private-service-connect).

## Prerequisites

* A PlanetScale Postgres database in a GCP region
* A GCP VPC in the same region where you want to establish the private connection
* Appropriate GCP IAM permissions to create Private Service Connect endpoints
* **Required APIs enabled**: Cloud DNS API and Service Directory API must be enabled in your project for automatic DNS zone creation (see [GCP documentation](https://cloud.google.com/vpc/docs/dns-vpc-hosted-services#auto-dns-consumer))

## Establishing a Private Service Connect endpoint

1. **Retrieve the Private Service Name**:

   1. From the PlanetScale organization dashboard, select the desired database
   2. Navigate to **Settings** from the menu on the left
   3. Select **Roles**
   4. Click on a role with permissions to the relevant `Branch`
   5. Copy the `Private Host` and `Private Service Name` from the role details

   <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/psc-private-host-names.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=039af70e430f615759d661889f2975c3" alt="Private connection strings" width="1814" height="356" data-path="postgres/connecting/private-connections/psc-private-host-names.png" />

   Save these two attributes for your records and the rest of the configuration.

   <Note>
     Both the `Private Host` and `Private Service Name` values are the same for all roles for a given PlanetScale database `Branch`. Once enabled, any role can use the Private Service Connect endpoint. You do not need to configure this per PlanetScale `Role`.
   </Note>

2. **Enable required APIs**: Ensure Cloud DNS and Service Directory APIs are enabled for automatic DNS zone creation:

   Via GCP Console:

   1. Confirm you are in the proper `<gcp-region>` from the project selector
   2. From the top search bar, search for `CloudDNS` and select it from the results
   3. Click to enable the API
   4. From the top search bar, search for `Service Directory` and select it from the results
   5. Click to enable the API

3. **Navigate to Private Service Connect**: In the GCP Console:

   1. Confirm you are in the proper `<gcp-region>` from the project selector
   2. From the top search bar, search for `Private Service Connect` and select it from the results

   <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/psc-search.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=1512642665fe31e4be2881493cfe2f01" alt="Private Service Connect console" width="1416" height="648" data-path="postgres/connecting/private-connections/psc-search.png" />

4. **Create a new endpoint**: Click "**+ Connect endpoint**".

5. **Configure endpoint details**:

   * **Target**: Select "Published Service"
   * **Target Service**: Enter the `Private Service Name` recorded from the PlanetScale Dashboard
   * **Endpoint name**: Choose a descriptive name (e.g., "planetscale-main" uses the `Branch` name in it. This name will be part of the connection host string you use going forward)
   * **Network**: Select your VPC network
   * **Subnet**: Choose a subnet that your application servers can access
   * **Create an IP Address**: Reserve a static IP address for the endpoint
   * **Enable Global Access**: Recommended - allows applications in other regions to reach the endpoint
   * **Create a namespace**: Recommended - Set a namespace in `Service Directory` to enable creation of an entry for this endpoint

   <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/psc-endpoint-configuration.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=9550639d2cf4a5f50845ae8c17846d62" alt="Endpoint configuration details" width="1180" height="1914" data-path="postgres/connecting/private-connections/psc-endpoint-configuration.png" />

6. **Create the endpoint**: Click "**Add Endpoint**" and wait for the endpoint status to show "Accepted" (this may take several minutes).

## Verifying your Private Service Connect endpoint connectivity

1. **Confirm endpoint status**: In the GCP Console, verify that your endpoint's status shows "Accepted".

   <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/psc-active-endpoint.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=56ab9c43aae6344f38c047687ff401a8" alt="Active Endpoint" width="2164" height="1454" data-path="postgres/connecting/private-connections/psc-active-endpoint.png" />

2. **Test DNS resolution**: If Cloud DNS is enabled, GCP automatically creates a private Cloud DNS zone for your endpoint. The DNS zone will match the `Private Host` (recorded earlier from the PlanetScale Dashboard).

   You can verify by navigating to the `Cloud DNS` page from the left Nav:

   <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/psc-clouddns-record.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=121bfa531bf8fa5e8006365685bbb318" alt="Cloud DNS resource" width="2326" height="874" data-path="postgres/connecting/private-connections/psc-clouddns-record.png" />

   To address the actual host endpoint you will use, you need to combine the `Endpoint name` you defined with the DNS zone name here (same as the `Private Host` recorded earlier).

   From this example:

   * `Endpoint name` = planetscale-main
   * `Private Host` = gcp-us-central1-1.private-pg.psdb.cloud

   Which results in:

   ```
   planetscale-main.gcp-us-central1-1.private-pg.psdb.cloud
   ```

   To confirm DNS is working properly in your VPC, run a DNS lookup from a VM instance in your configured VPC:

   ```bash theme={null}
   dig +short planetscale-main.gcp-us-central1-1.private-pg.psdb.cloud
   10.128.0.17
   ```

3. **Test your PostgreSQL connection**:

   Once you have confirmed DNS resolution, test the private endpoint:

   ```bash theme={null}
   psql 'host=planetscale-main.gcp-us-central1-1.private-pg.psdb.cloud port=5432 user=postgres.XYZ234 password=pscale_pw_REDACTED dbname=postgres sslmode=require'
   ```

## Update your connection strings

Once your Private Service Connect endpoint is established and verified, update your application's connection strings to use the private endpoint address. Note that you need both the `Endpoint name` you configured and the `Private Host` from the PlanetScale dashboard to form the full hostname for your application to use.

* **Original**: `gcp-us-central1-1.pg.psdb.cloud`
* **Private**: `planetscale-main.gcp-us-central1-1.private-pg.psdb.cloud`

Replace the hostname in your connection strings while keeping all other parameters (user, password, database name, etc.) the same.

## VPC considerations

Your VPC configuration should allow:

* **Private Google Access**: Enable this if your compute instances don't have external IP addresses
* **Subnet routing**: Ensure proper routing between your application subnets and the PSC endpoint subnet
* **Network tags**: Use network tags to organize and control access to your PSC endpoint

## Restricting access to Private Service Connect only

By default, setting up GCP Private Service Connect does not block public internet access to your database. If you want to ensure your database only accepts connections through Private Service Connect, you can use [IP restrictions](../ip-restrictions.md) to block public internet traffic.

When connections come through Private Service Connect, PlanetScale sees the source IP as coming from our internal network infrastructure (private IP ranges), not from your VPC's CIDR range. To restrict access to Private Service Connect-only connections, create an IP restriction rule allowing the RFC1918 private ranges:

```
10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16
```

This configuration blocks all public internet connections while allowing traffic through your Private Service Connect endpoint.

For more details on configuring IP restrictions, see our [IP restrictions documentation](../ip-restrictions.md).

## Troubleshooting

If you're experiencing connectivity issues:

1. **Verify endpoint status**: Ensure your Private Service Connect endpoint shows "Accepted" status
2. **Test DNS resolution**: Verify DNS is resolving to the private IP address in your VPC
3. **Check VPC routing**: Ensure there are no route conflicts or missing routes
4. **Verify network connectivity**: Use tools like `telnet` or `nc` to test port connectivity
5. **Contact support**: If issues persist, contact PlanetScale support with your endpoint configuration details

## Next steps

* [Learn about PostgreSQL roles and permissions](../roles.md)
* [Configure connection pooling with PgBouncer](../pgbouncer.md)
* [Monitor your connections and performance](../../monitoring/query-insights.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
