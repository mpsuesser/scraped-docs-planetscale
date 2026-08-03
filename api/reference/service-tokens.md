---
url: https://planetscale.com/docs/api/reference/service-tokens
title: "Service Tokens"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Overview

This document will show you how to create a service token for the API in PlanetScale. All PlanetScale API endpoints require one. You can read more about using service tokens outside of the API in the [PlanetScale service token documentation](service-tokens.md).

## Authorization header

To make requests to the API, add the service token values in the `Authorization` header in your HTTP API request using the following format:

```text
Authorization: <SERVICE_TOKEN_ID>:<SERVICE_TOKEN>
```

Here is a cURL example:

```text
curl --request GET 'https://api.planetscale.com/v1/organizations' \
--header 'Authorization: <SERVICE_TOKEN_ID>:<SERVICE_TOKEN>'
```

## Creating a service token

To create a service token using the dashboard, log into your organization and click **Settings > Service tokens > New service token**.

Give the token a descriptive name (this is used for your reference only) and click **Create service token**.

### Service token ID

Copy this value to use as `SERVICE_TOKEN_ID`.

The ID is also visible on the service token page after you continue to token permissions.

### Service token

The token is generated immediately after the service token is created.

Copy this value to use as `SERVICE_TOKEN`.

![Modal showing service token ID and token secret](https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/images/reference/ef1a137-new-service-token.png?w=2500&fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=035d9d0bb3b91bd1e4e3938e085e299c)

Modal showing service token ID and token secret

## Access permissions

You can access your specific service token page from your organization’s **Settings > Service token** page.

Service tokens are configured with granular permissions for both organizations and databases access. On the page for your specific service token you can add one or many of the following permissions.

![Showing the UI for organization and database access with buttons to add what access permissions](https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/images/reference/858081d-service-token-accesses.png?w=2500&fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=69bf65b208034fd66f2b3b1291d7f96e)

Showing the UI for organization and database access with buttons to add what access permissions

Please note that you may only add service token accesses that you are also authorized to do. For example, as an organization member, you can’t create a service token with `create_databases` access.

### Organization access permissions

A service token can have granular permissions across an organization with one or multiple of the following access permissions.

Check the box next to each permission option you need to grant. Once you are done, click **Save permissions**.

| Access permissions | Description |
| --- | --- |
| read\_organization | Get information about an organization |
| read\_invoices | Get invoices for an organization |
| read\_audit\_logs | Get audit logs for an organization |
| read\_databases | Get information about an organization’s databases |
| create\_databases | Create organization databases |
| delete\_databases | Delete organization databases |
| read\_oauth\_applications | Get information about an organization’s OAuth applications and their tokens |
| read\_oauth\_tokens | Get OAuth tokens for an OAuth application |
| write\_oauth\_tokens | Create and refresh OAuth grants and tokens for an OAuth application |
| delete\_oauth\_tokens | Delete OAuth tokens for an OAuth application |
| read\_service\_tokens | Get information about service tokens |
| write\_service\_tokens | Create and update service tokens |
| delete\_service\_tokens | Delete service tokens |
| write\_teams | Create and update teams |
| read\_metrics\_endpoints | Get information about branch metrics endpoints |

### Database access permissions

A service token can have granular permissions across a database with one or multiple of the following access permissions.

Select the database you want to grant access for and check the box next to each permission option you need to grant. Once you are done, click **Save permissions**.

| Access permissions | Description |
| --- | --- |
| read\_database | Get information about a database |
| write\_database | Update information about a database |
| delete\_database | Delete a database |
| create\_branch | Create a database branch |
| read\_branch | Read a database branch |
| delete\_branch | Delete a database branch |
| delete\_production\_branch | Delete a production database branch |
| connect\_branch | Connect to a database branch |
| connect\_production\_branch | Connect to a production database branch |
| delete\_branch\_password | Delete a password for a non-production branch |
| delete\_production\_branch\_password | Delete a password for a production branch |
| create\_deploy\_request | Create a deploy request |
| read\_deploy\_request | Read a deploy request |
| approve\_deploy\_request | Approve a deploy request |
| create\_comment | Create a deploy request comment |
| read\_comment | Read a deploy request comment |
| read\_backups | List backups |
| write\_backups | Create and update backups |
| delete\_backups | Delete development branch backups |
| delete\_production\_branch\_backups | Delete production branch backups |
| restore\_backup | Restore a development branch backup |
| restore\_production\_branch\_backup | Restore a production branch backup |
| write\_branch\_vschema | Update the VSchema for a database branch |
| write\_production\_branch\_vschema | Update the VSchema for a production database branch |

When a service token with `create_databases` organization access creates a database, it is automatically granted database access permissions for that database. See [Creating databases with service tokens](../service-tokens.md#creating-databases-with-service-tokens) for more information.

## Service tokens and deploy requests approvals

When a database requires administrator approval for deploy requests (located in your database’s Settings page), a service token cannot approve a deploy request created by the same service token. Also, users can’t approve a deploy request created by a service token that they created.

## Example

### Creating a PlanetScale branch with service tokens

Creating a database branch is one of the many API endpoints documented in our [PlanetScale API docs](create_branch.md).

The following steps are required to make a successful API request:

### Potential errors

If you get a `{"code":"not_found","message":"Not Found"}` response, it is likely you either did not change the variables in the example cURL request or you did not set the access permissions correctly for the service token.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
