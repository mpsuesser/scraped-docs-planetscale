---
url: https://planetscale.com/docs/plans/managed/security-and-access/user-management
title: "User Management"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# User management

## Initial onboarding

### Administrative onboarding

The customer's initial administrative user creates an organization on PlanetScale. Administrative accounts have the `Organization Administrator` role assigned.

### User onboarding

Users can be onboarded either manually or using single sign-on. Manual onboarding is handled by the Administrator once they are initially granted access. Users managed via SSO are onboarded once the SSO provider is connected and configured.

### Single sign-on

PlanetScale Managed requires single sign-on (SSO) for the API and web interface, enabling organizations to manage access through their existing directory services.

You can read more about single sign-on and how to set it up in the [PlanetScale single sign-on documentation](../../../security/sso.md).

## Access levels

PlanetScale currently supports three different roles inside of organizations:

* `Organization Administrator`
* `Organization Member`
* `Database Administrator`

See the [PlanetScale access control documentation](../../../security/access-control.md) for a further breakdown of each role's permissions.

## Separation of accounts

PlanetScale Managed provides integration with numerous single sign-on providers. Users can have entirely separate personal and corporate accounts with PlanetScale when their organization uses SSO.

It is up to the customer to ensure that they maintain their SSO setup and do not invite or allow employees to use any other authentication method to access PlanetScale.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
