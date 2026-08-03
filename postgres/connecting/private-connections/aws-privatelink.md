---
url: https://planetscale.com/docs/postgres/connecting/private-connections/aws-privatelink
title: "Aws Privatelink"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

[AWS PrivateLink](https://aws.amazon.com/privatelink/) is a highly available, scalable technology that enables you to privately connect your VPC to supported AWS services, VPC endpoint services, and AWS Marketplace partner services.

### When to use AWS PrivateLink

By default, PlanetScale Postgres databases use secure connections over the public internet with industry-standard TLS encryption. This approach is secure and meets the needs of most customers. However, you may want to consider AWS PrivateLink if:

- **Compliance requirements**: Your organization has stronger regulatory or compliance mandates that require database connections to avoid the public internet entirely
- **Enhanced security posture**: You want an additional layer of network isolation for sensitive data workloads
- **Network architecture**: Your existing AWS infrastructure is designed around private connectivity patterns
- **Reduced network latency**: AWS PrivateLink can help reduce latency by avoiding the extra network hop through a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) that’s typically required for outbound internet connections from private subnets. While this latency difference is often minimal (typically single-digit milliseconds), it may be noticeable if you’re migrating from a database that was previously hosted directly within your VPC

AWS PrivateLink provides these security and compliance benefits by ensuring your database traffic never leaves the AWS backbone network.

Normal PlanetScale Postgres connectivity (as described in our [standard connection documentation](../../connecting.md)) uses secure TLS encryption over the public internet and is appropriate for most use cases. AWS PrivateLink is primarily beneficial for compliance and enhanced security requirements.

### PrivateLink pricing

PlanetScale charges a flat rate of **$0.01/GB** for all network traffic (both egress and ingress) over private connections. This replaces the standard [egress pricing](../../pricing.md#network-data-transfer) that applies to public connections. Each branch has a default amount of included private network traffic before this rate takes effect.

AWS also charges standard PrivateLink pricing for VPC endpoints, which includes:

| Charge Type | Rate | Description |
| --- | --- | --- |
| **VPC endpoint hourly charges** | ~$0.01 per hour | Per VPC endpoint (varies by region) |
| **Data processing charges** | ~$0.01 per GB | Data processed through the VPC endpoint (varies by region) |

For current pricing in your region, see the [AWS PrivateLink pricing page](https://aws.amazon.com/privatelink/pricing/).

## Prerequisites

- A PlanetScale Postgres database in an AWS region
- An AWS VPC in the same region where you want to establish the private connection
- Appropriate AWS IAM permissions to create VPC endpoints (see [AWS VPC endpoint permissions documentation](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-iam.html))
- Appropriate AWS IAM permissions to create and modify Security Groups (see [AWS IAM permissions for security groups documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-policies-for-amazon-ec2.html#iam-policies-security-groups))

## Establishing a VPC endpoint

1. **Retrieve the Private Service Name**:
	1. From the PlanetScale organization dashboard, select the desired database
		2. Navigate to **Settings** from the menu on the left
		3. Select **Roles**
		4. Click on a role with permissions to the relevant `Branch`
		5. Copy the `Private Host` and `Private Service Name` from the role details
![Private connection strings](https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/aws-private-host-names.png?w=2500&fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=d947f955316088c50bfb22745dc94efe)

Private connection strings

Save these two attributes for your records and the rest of the configuration.

Both the `Private Host` and `Private Service Name` values are the same for all roles for a given PlanetScale database `Branch`. Once enabled, any role can use the PrivateLink endpoint. You do not need to configure this per PlanetScale `Role`.

1. **Create a Security Group for the Endpoint**: You will need an AWS Security Group configured to allow inbound traffic for the required ports. You can configure access using either the security group ID of your application hosts, your VPC’s CIDR configuration, or specific subnet CIDR configurations. Ensure your [security groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html) allow:
	- **Inbound PostgreSQL (port 5432)**: For direct database connections
		- **Inbound PgBouncer (port 6432)**: For pooled connections via PgBouncer
	An example using the AWS CLI:
	```shellscript
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
	```shellscript
	aws ec2 describe-vpcs --query 'Vpcs[*].[VpcId,CidrBlock]' --output table
	```
2. **Navigate to VPC Endpoints**: In your AWS Console:
	1. Confirm you are in the proper `<aws-region>` from the dropdown on the top right
		2. In the search field at the top left enter “Endpoints”.
		3. Click the link listed as a **VPC Feature**.
	![Endpoint search](https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/endpoint-search.png?w=2500&fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=4c0fc90daffb113828b68b759518c6f8)
	Endpoint search
3. **Create a new endpoint**: Click “ **Create Endpoint** ”.
	![Create a new endpoint](https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/create-new-endpoint.png?w=2500&fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=a1957dd7119e4016a5b5ee664b2b8973)
	Create a new endpoint
4. **Select endpoint type**: Choose “Endpoint services that use NLBs and GWLBs”.
	![Menu to select endpoint type](https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/type-of-endpoint.png?w=2500&fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=c8dbdb40ce2e0f70f05c6003712e7dd1)
	Menu to select endpoint type
5. **Enter service name**: Enter in the “Service name” text box the `Private Service Name` retrieved from the PlanetScale dashboard. Click “ **Verify service** ” to confirm the service exists.
	![Endpoint service name and verification](https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/verified-endpoint.png?w=2500&fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=d1982301906d2aec53e49f2bf8eac174)
	Endpoint service name and verification
6. **Configure VPCs**: Choose the VPC that should have access to the PlanetScale service endpoint.
7. **Enable DNS names**: Click the “Additional settings” dropdown arrow to reveal DNS configuration options, and select the “ **Enable DNS name** ” checkbox.
8. **Configure Subnets**: Choose the subnets that should have endpoint interfaces for the PlanetScale service endpoint. It is recommended that you select at least 2. You should select subnets that your application servers have access to.
![Subnets](https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/network-subnets-config.png?w=2500&fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=f403e28b00d1c9a21970fd2231674546)
9. **Configure security groups**: Choose the appropriate security group to control which resources can send traffic to the PlanetScale service endpoint. Use the one created earlier if you created one for this purpose.
![Security Groups](https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/security-groups.png?w=2500&fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=0c212f505c8237537dc65f8a0f26ee83)

Security Groups

10. **Create the endpoint**: Click “ **Create endpoint** ” and wait for the VPC endpoint status to show “Available” (this may take several minutes).
![Available Endpoint](https://mintcdn.com/planetscale-2/o_cHHlFu3sW-NBEp/postgres/connecting/private-connections/available-endpoint.png?w=2500&fit=max&auto=format&n=o_cHHlFu3sW-NBEp&q=85&s=c04c24a43958701069bb7554258b43ae)

Available Endpoint

## Verifying your VPC endpoint connectivity

1. **Confirm endpoint status**: In the AWS Console, verify that your endpoint’s status shows “Available”.
2. **Test DNS resolution**: From an EC2 instance in your configured VPC, run a DNS lookup to confirm resolution to your VPC’s IP range. Use the `Private Host` you recorded earlier from the PlanetScale dashboard:
	```shellscript
	dig +short <YOUR ENDPOINT>.private-pg.psdb.cloud
	10.0.2.120
	10.0.1.118
	```
3. **Test your new connection**:
	Once you have confirmed DNS resolution, test the private endpoint:
	```shellscript
	psql 'host=<YOUR ENDPOINT>.private-pg.psdb.cloud port=5432 user=postgres.XYZ234 password=pscale_pw_REDACTED dbname=postgres sslnegotiation=direct sslmode=verify-full sslrootcert=system'
	```

## Update your connection strings

Once your VPC endpoint is established and verified, you’re ready to update your application’s connection strings to use the private endpoint address instead of the standard public endpoint.

## Security group considerations

Ensure your [security groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html) allow:

- **Outbound PostgreSQL (port 5432)**: For direct database connections
- **Outbound PgBouncer (port 6432)**: For pooled connections via PgBouncer
- **Inbound - any application-specific ports**: Based on your connection requirements

For more details about connection types and when to use each port, see our [connection documentation](../../connecting.md) and [PgBouncer guide](../pgbouncer.md).

## Network ACL considerations

VPC [Network ACLs (NACLs)](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html) operate at the subnet level and provide an additional layer of security beyond security groups. For AWS PrivateLink connections to PlanetScale, ensure your NACLs allow:

- **Outbound PostgreSQL (ports 5432, 6432)**: For database connections
- **Ephemeral ports (1024-65535)**: For return traffic from AWS PrivateLink endpoints

Most default NACL configurations allow all outbound traffic and are compatible with PrivateLink. If using custom restrictive NACLs, add explicit allow rules for the above ports.

## Restricting access to PrivateLink only

By default, setting up AWS PrivateLink does not block public internet access to your database. If you want to ensure your database only accepts connections through PrivateLink, you can use [IP restrictions](../ip-restrictions.md) to block public internet traffic.

When connections come through PrivateLink, PlanetScale sees the source IP as coming from our internal network infrastructure (private IP ranges), not from your VPC’s CIDR range. To restrict access to PrivateLink-only connections, create an IP restriction rule allowing the RFC1918 private ranges:

```text
10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16
```

This configuration blocks all public internet connections while allowing traffic through your PrivateLink endpoint.

For more details on configuring IP restrictions, see our [IP restrictions documentation](../ip-restrictions.md).

## Troubleshooting

If you’re experiencing connectivity issues:

1. **Verify endpoint status**: Ensure your VPC endpoint shows “Available” status
2. **Check security groups**: Confirm your security groups allow the required ports
3. **Check NACLs**: Confirm that your VPC’s NACLs are configured to allow the correct network traffic
4. **Test DNS resolution**: Verify DNS is resolving to private IP addresses in your VPC CIDR range
5. **Use AWS Reachability Analyzer**: The [Reachability Analyzer](https://docs.aws.amazon.com/vpc/latest/reachability/what-is-reachability-analyzer.html) allows you to inspect the path between two resources (such as a client and your PlanetScale Postgres endpoint) and provides guidance on why connectivity might be failing
6. **Contact support**: If issues persist, contact PlanetScale support with your endpoint configuration details

## Next steps

- [Learn about PostgreSQL roles and permissions](../roles.md)
- [Configure connection pooling with PgBouncer](../pgbouncer.md)
- [Monitor your connections and performance](../../monitoring/query-insights.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
