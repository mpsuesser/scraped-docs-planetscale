---
url: https://planetscale.com/docs/plans/deployment-options
title: "Deployment Options"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

**Multi-tenancy** — In a multi-tenant environment, your database is hosted on infrastructure shared with other users/customers.

**Single-tenancy** — In a single-tenant environment, your database is hosted on dedicated infrastructure that is not shared with any other users/customers.

Your deployment options depend on the [PlanetScale plan](../planetscale-plans.md) you choose. This documentation covers some differences between single-tenancy and multi-tenancy so you can evaluate which plan may be suitable for you. For an in-depth look at the different plans we offer, see the [PlanetScale plans documentation](../planetscale-plans.md).

## Multi-tenancy deployment on PlanetScale

Multi-tenancy is the default deployment option. When you sign up for a PlanetScale account with the Base plan, your databases will be created in our multi-tenancy deployment offering. Our Enterprise plan also offers a multi-tenancy deployment option.

### Multi-tenancy highlights

- Your database runs on PlanetScale’s [secure cloud infrastructure](../security.md)
- Lowest cost plans
- No configuration requirements on your end
- BAAs available for HIPAA compliance
- [Private connection support](../vitess/connecting/private-connections.md) via AWS PrivateLink
- [Private connection support](../vitess/connecting/private-connections-gcp.md) via GCP Private Service Connect

## Single-tenancy deployment on PlanetScale

Single-tenant deployment options are available with [PlanetScale Enterprise](../planetscale-plans.md#enterprise-plan). Companies that require their PlanetScale databases to be hosted in a single-tenant environment have two options: Enterprise — Single-tenant and Managed.

In both options, your database is deployed in a single-tenant environment. The main difference between Enterprise — Single-tenant and Managed is who owns the underlying account. With [Managed](managed.md), your database is deployed in your own AWS/GCP account. With Enterprise — Single-tenant, PlanetScale owns and manages the account.

If you’re interested in learning more, please [reach out](https://planetscale.com/contact), and we can figure out the best solution for your use case.

### Single-tenancy highlights

- Your resources run on a separate isolated AWS or GCP account, apart from other customers
- Your databases can be deployed to any cloud provider region you choose that offers three Availability Zones, even [regions](https://planetscale.com/docs/vitess/regions) that PlanetScale does not offer on our self-serve plans
- You can continue to use the PlanetScale UI and CLI in the same way that you would our general self-serve offerings, but the infrastructure runs in an isolated environment
- BAAs available for HIPAA compliance
- Support for private database connectivity via:

If you have any questions about which deployment option may be best for you, [reach out to us](https://planetscale.com/contact) and we can help you figure it out.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
