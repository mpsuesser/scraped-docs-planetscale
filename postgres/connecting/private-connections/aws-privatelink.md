---
url: https://planetscale.com/docs/postgres/connecting/private-connections/aws-privatelink
title: "Aws Privatelink"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Connect privately with AWS PrivateLink

> When you use AWS PrivateLink, your network traffic between your VPC and PlanetScale stays within the AWS network, without traversing the public internet.

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

<PlatformAvailability current="postgres" vitess="/vitess/connecting/private-connections" />

[AWS PrivateLink](https://aws.amazon.com/privatelink/) is a highly available, scalable technology that enables you to privately connect your VPC to supported AWS services, VPC endpoint services, and AWS Marketplace partner services.

### When to use AWS PrivateLink

By default, PlanetScale Postgres databases use secure connections over the public internet with industry-standard TLS encryption. This approach is secure and meets the needs of most customers. However, you may want to consider AWS PrivateLink if:

* **Compliance requirements**: Your organization has stronger regulatory or compliance mandates that require database connections to avoid the public internet entirely
* **Enhanced security posture**: You want an additional layer of network isolation for sensitive data workloads
* **Network architecture**: Your existing AWS infrastructure is designed around private connectivity patterns
* **Reduced network latency**: AWS PrivateLink can help reduce latency by avoiding the extra network hop through a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) that's typically required for outbound internet connections from private subnets. While this latency difference is often minimal (typically single-digit milliseconds), it may be noticeable if you're migrating from a database that was previously hosted directly within your VPC

AWS PrivateLink provides these security and compliance benefits by ensuring your database traffic never leaves the AWS backbone network.

<Note>
  Normal PlanetScale Postgres connectivity (as described in our [standard connection documentation](../../connecting.md)) uses secure TLS encryption over the public internet and is appropriate for most use cases. AWS PrivateLink is primarily beneficial for compliance and enhanced security requirements.
</Note>

### PrivateLink pricing

PlanetScale charges a flat rate of **\$0.01/GB** for all network traffic (both egress and ingress) over private connections.
This replaces the standard [egress pricing](../../pricing.md#network-data-transfer) that applies to public connections.
Each branch has a default amount of included private network traffic before this rate takes effect.

AWS also charges standard PrivateLink pricing for VPC endpoints, which includes:

| Charge Type                     | Rate              | Description                                                |
| ------------------------------- | ----------------- | ---------------------------------------------------------- |
| **VPC endpoint hourly charges** | \~\$0.01 per hour | Per VPC endpoint (varies by region)                        |
| **Data processing charges**     | \~\$0.01 per GB   | Data processed through the VPC endpoint (varies by region) |

For current pricing in your region, see the [AWS PrivateLink pricing page](https://aws.amazon.com/privatelink/pricing/).

## Prerequisites

* A PlanetScale Postgres database in an AWS region
* An AWS VPC in the same region where you want to establish the private connection
* Appropriate AWS IAM permissions to create VPC endpoints (see [AWS VPC endpoint permissions documentation](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-iam.html))
* Appropriate AWS IAM permissions to create and modify Security Groups (see [AWS IAM permissions for security groups documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-policies-for-amazon-ec2.html#iam-policies-security-groups))

## Establishing a VPC endpoint

1. **Retrieve the Private Service Name**:
   1. From the PlanetScale organization dashboard, select the desired database
   2. Navigate to **Settings** from the menu on the left
   3. Select **Roles**
   4. Click on a role with permissions to the relevant `Branch`
   5. Copy the `Private Host` and `Private Service Name` from the role details

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/aws-private-host-names.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=1b5f557ed732b7f5929e949b76848114" alt="Private connection strings" width="1842" height="402" data-path="postgres/connecting/private-connections/aws-private-host-names.png" />

Save these two attributes for your records and the rest of the configuration.

<Note>
  Both the `Private Host` and `Private Service Name` values are the same for all roles for a given PlanetScale database `Branch`. Once enabled, any role can use the PrivateLink endpoint. You do not need to configure this per PlanetScale `Role`.
</Note>

1. **Create a Security Group for the Endpoint**:
   You will need an AWS Security Group configured to allow inbound traffic for the required ports. You can configure access using either the security group ID of your application hosts, your VPC's CIDR configuration, or specific subnet CIDR configurations. Ensure your [security groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html) allow:

   * **Inbound PostgreSQL (port 5432)**: For direct database connections
   * **Inbound PgBouncer (port 6432)**: For pooled connections via PgBouncer

   An example using the AWS CLI:

   ```bash theme={null}
   # Create the security group (capture its ID)
   SG_ID=$(aws ec2 create-security-group \
   --group-name PScalePrivateLinkEndpointSG \
   --description "Security group for PlanetScale PrivateLink endpoint" \
   --vpc-id <your-vpc-id> \
   --query 'GroupId' --output text)

   # Option A (preferred): allow only from a client SG (replace sg-CLIENT)
   aws ec2 authorize-security-group-ingress \
      --group-id "$SG_ID" \
      --ip-permissions '[
        {"IpProtocol":"tcp","FromPort":5432,"ToPort":5432,"UserIdGroupPairs":[{"GroupId":"sg-CLIENT"}]},
        {"IpProtocol":"tcp","FromPort":6432,"ToPort":6432,"UserIdGroupPairs":[{"GroupId":"sg-CLIENT"}]}
      ]'

   # Option B: allow from entire VPC CIDR (replace with your actual CIDR)
   #aws ec2 authorize-security-group-ingress \
   #--group-id "$SG_ID" \
   #--ip-permissions '[
   #   {"IpProtocol":"tcp","FromPort":5432,"ToPort":5432,"IpRanges":[{"CidrIp":"10.0.0.0/16"}]},
   #   {"IpProtocol":"tcp","FromPort":6432,"ToPort":6432,"IpRanges":[{"CidrIp":"10.0.0.0/16"}]}
   #]'
   ```

   Replace `<your-vpc-id>` with your actual VPC ID. You can find your VPC ID and its CIDR block using:

   ```bash theme={null}
   aws ec2 describe-vpcs --query 'Vpcs[*].[VpcId,CidrBlock]' --output table
   ```

2. **Navigate to VPC Endpoints**: In your AWS Console:

   1. Confirm you are in the proper `<aws-region>` from the dropdown on the top right
   2. In the search field at the top left enter "Endpoints".
   3. Click the link listed as a **VPC Feature**.

   <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/endpoint-search.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=056b9b38fd36f778e9551a17c89cb37b" alt="Endpoint search" width="2598" height="872" data-path="postgres/connecting/private-connections/endpoint-search.png" />

3. **Create a new endpoint**: Click "**Create Endpoint**".

   <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/create-new-endpoint.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=7a530c11ec6ab94a467203f45e0369ea" alt="Create a new endpoint" width="2598" height="874" data-path="postgres/connecting/private-connections/create-new-endpoint.png" />

4. **Select endpoint type**: Choose "Endpoint services that use NLBs and GWLBs".

   <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/type-of-endpoint.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=3d06cd7cc0a99c988fbe6c6a7f698e9a" alt="Menu to select endpoint type" width="2588" height="1280" data-path="postgres/connecting/private-connections/type-of-endpoint.png" />

5. **Enter service name**: Enter in the "Service name" text box the `Private Service Name` retrieved from the PlanetScale dashboard. Click "**Verify service**" to confirm the service exists.

   <img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/verified-endpoint.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=e689939f8891b0ebb649774c200c4910" alt="Endpoint service name and verification" width="1994" height="548" data-path="postgres/connecting/private-connections/verified-endpoint.png" />

6. **Configure VPCs**: Choose the VPC that should have access to the PlanetScale service endpoint.

7. **Enable DNS names**: Click the "Additional settings" dropdown arrow to reveal DNS configuration options, and select the "**Enable DNS name**" checkbox.

8. **Configure Subnets**: Choose the subnets that should have endpoint interfaces for the PlanetScale service endpoint. It is recommended that you select at least 2. You should select subnets that your application servers have access to.

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/network-subnets-config.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=7bc08b7d82f5f6b3697de4bad729d932" alt="Subnets" width="1978" height="1632" data-path="postgres/connecting/private-connections/network-subnets-config.png" />

9. **Configure security groups**: Choose the appropriate security group to control which resources can send traffic to the PlanetScale service endpoint. Use the one created earlier if you created one for this purpose.

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/security-groups.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=d12a4e463598ec26ae735a0c8b03800a" alt="Security Groups" width="2710" height="514" data-path="postgres/connecting/private-connections/security-groups.png" />

10. **Create the endpoint**: Click "**Create endpoint**" and wait for the VPC endpoint status to show "Available" (this may take several minutes).

<img src="https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/available-endpoint.png?fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=e7f0a0f31f851f90f8b245460b1f9e16" alt="Available Endpoint" width="1602" height="1098" data-path="postgres/connecting/private-connections/available-endpoint.png" />

## Verifying your VPC endpoint connectivity

1. **Confirm endpoint status**: In the AWS Console, verify that your endpoint's status shows "Available".

2. **Test DNS resolution**: From an EC2 instance in your configured VPC, run a DNS lookup to confirm resolution to your VPC's IP range. Use the `Private Host` you recorded earlier from the PlanetScale dashboard:

   ```bash theme={null}
   dig +short <YOUR ENDPOINT>.private-pg.psdb.cloud
   10.0.2.120
   10.0.1.118
   ```

3. **Test your new connection**:

   Once you have confirmed DNS resolution, test the private endpoint:

   ```bash theme={null}
   psql 'host=<YOUR ENDPOINT>.private-pg.psdb.cloud port=5432 user=postgres.XYZ234 password=pscale_pw_REDACTED dbname=postgres sslnegotiation=direct sslmode=verify-full sslrootcert=system'
   ```

## Update your connection strings

Once your VPC endpoint is established and verified, you're ready to update your application's connection strings to use the private endpoint address instead of the standard public endpoint.

## Security group considerations

Ensure your [security groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html) allow:

* **Outbound PostgreSQL (port 5432)**: For direct database connections
* **Outbound PgBouncer (port 6432)**: For pooled connections via PgBouncer
* **Inbound - any application-specific ports**: Based on your connection requirements

For more details about connection types and when to use each port, see our [connection documentation](../../connecting.md) and [PgBouncer guide](../pgbouncer.md).

## Network ACL considerations

VPC [Network ACLs (NACLs)](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html) operate at the subnet level and provide an additional layer of security beyond security groups. For AWS PrivateLink connections to PlanetScale, ensure your NACLs allow:

* **Outbound PostgreSQL (ports 5432, 6432)**: For database connections
* **Ephemeral ports (1024-65535)**: For return traffic from AWS PrivateLink endpoints

Most default NACL configurations allow all outbound traffic and are compatible with PrivateLink. If using custom restrictive NACLs, add explicit allow rules for the above ports.

## Restricting access to PrivateLink only

By default, setting up AWS PrivateLink does not block public internet access to your database. If you want to ensure your database only accepts connections through PrivateLink, you can use [IP restrictions](../ip-restrictions.md) to block public internet traffic.

When connections come through PrivateLink, PlanetScale sees the source IP as coming from our internal network infrastructure (private IP ranges), not from your VPC's CIDR range. To restrict access to PrivateLink-only connections, create an IP restriction rule allowing the RFC1918 private ranges:

```
10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16
```

This configuration blocks all public internet connections while allowing traffic through your PrivateLink endpoint.

For more details on configuring IP restrictions, see our [IP restrictions documentation](../ip-restrictions.md).

## Troubleshooting

If you're experiencing connectivity issues:

1. **Verify endpoint status**: Ensure your VPC endpoint shows "Available" status
2. **Check security groups**: Confirm your security groups allow the required ports
3. **Check NACLs**: Confirm that your VPC's NACLs are configured to allow the correct network traffic
4. **Test DNS resolution**: Verify DNS is resolving to private IP addresses in your VPC CIDR range
5. **Use AWS Reachability Analyzer**: The [Reachability Analyzer](https://docs.aws.amazon.com/vpc/latest/reachability/what-is-reachability-analyzer.html) allows you to inspect the path between two resources (such as a client and your PlanetScale Postgres endpoint) and provides guidance on why connectivity might be failing
6. **Contact support**: If issues persist, contact PlanetScale support with your endpoint configuration details

## Next steps

* [Learn about PostgreSQL roles and permissions](../roles.md)
* [Configure connection pooling with PgBouncer](../pgbouncer.md)
* [Monitor your connections and performance](../../monitoring/query-insights.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
