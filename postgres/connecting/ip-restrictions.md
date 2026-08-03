---
url: https://planetscale.com/docs/postgres/connecting/ip-restrictions
title: "Ip Restrictions"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

You can manage IP address restrictions for database connections in the “ **IP restrictions** ” tab under Settings for your database. IP restrictions control which networks can connect to your database, providing an additional layer of security beyond authentication.

IP restrictions restrict database connections to the specified IP ranges. Rules apply to all roles and schemas unless specified otherwise.

## Creating an IP restriction rule

You must be a database or organization administrator to create or modify an IP restriction rule.

## How IP restriction rules work

IP restrictions restrict database connections to the specified IP ranges. The behavior depends on how you configure each rule:

- **Apply to all roles and schemas**: Leave both the **Role** and **Schema** fields empty
- **Apply to specific role**: Specify a role name in the **Role** field to restrict connections for that role across all schemas
- **Apply to specific schema**: Specify a schema name in the **Schema** field to restrict connections to that schema from all roles
- **Apply to specific role and schema**: Specify both to create a rule that applies only when that role connects to that schema

Multiple rules can be created to build complex access policies for your database cluster.

## IP range format

The **IP ranges** field accepts:

- Individual IP addresses in CIDR notation (e.g., `1.2.3.4/32`)
- IP ranges in CIDR notation (e.g., `10.0.0.0/8`, `192.168.1.0/24`)
- Multiple entries separated by commas (e.g., `1.2.3.4/32, 10.0.0.0/8`)

## Editing an IP restriction rule

## Deleting an IP restriction rule

Deleting an IP restrictions rule is irreversible. After deletion, connections from those IP ranges will no longer be restricted, potentially allowing broader access to your database.

Rule creation and modification only applies to connections established after the change. It does not impact or disconnect existing connections, even if they break the newly-established rules.

## Using IP restrictions with private connections

If you’re connecting to your database through [AWS PrivateLink](private-connections/aws-privatelink.md) or [GCP Private Service Connect](private-connections/gcp-private-service-connect.md), the source IP address that PlanetScale sees is not your application’s IP address or your VPC’s CIDR range. Instead, PlanetScale sees the private IP address from our internal network infrastructure that handles the private connection.

### Restricting access to private connections only

If you want to ensure your database only accepts connections through PrivateLink or Private Service Connect (blocking all public internet access), you should configure your IP restrictions to allow the following RFC1918 private IP ranges:

| CIDR Range | Description |
| --- | --- |
| `10.0.0.0/8` | Class A private range |
| `172.16.0.0/12` | Class B private range |
| `192.168.0.0/16` | Class C private range |

By allowing only these private ranges, you effectively block all connections from the public internet while still allowing connections through your private endpoint.

Adding IP restrictions to your database is independent from setting up PrivateLink or Private Service Connect. Simply enabling a private connection does not automatically block public internet access. You must explicitly configure IP restrictions if you want to enforce private-only connectivity.

**Example configuration:**

To restrict a database to only accept connections through private endpoints, create an IP restriction rule with the following IP ranges:

```text
10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16
```

This configuration allows connections from any of the RFC1918 private IP ranges while blocking all public internet traffic.

## Best practices

When configuring IP restrictions rules:

- Start with the most restrictive rules that meet your requirements
- Use CIDR notation to define ranges efficiently (e.g., `/24` for a subnet rather than listing individual IPs)
- Document the purpose of each rule by using descriptive role names or organizing rules by application
- Regularly audit your IP restrictions rules to remove access that is no longer needed
- Consider creating separate roles for different applications or environments to enable fine-grained access control
- Complement network restrictions with [pg\_strict](../extensions/pg_strict.md) for query-level protection against accidental mass mutations

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
