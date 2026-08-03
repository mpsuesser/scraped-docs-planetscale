---
url: https://planetscale.com/docs/cli/service-tokens
title: "Service Tokens"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

Service tokens provide an alternate authentication method to be used with the PlanetScale CLI and API. They are typically used in automated scenarios where `pscale auth login` cannot be used. Service tokens are also required for any calls to the API, as well as minting OAuth tokens for API use.

## Create service tokens using the PlanetScale dashboard

To create a service token using the dashboard, log into your organization, go to the [**“Settings”** > **“Service tokens”**](https://app.planetscale.com/~/settings/service-tokens) page, and click the **“New service token”** button.

Give the token a name (this is used for your reference only). You can also optionally set an expiration date for the token — once the expiration date is reached, the token will stop working. Click **“Create service token”** to proceed.

The modal will update, displaying your service token where the Name field was. Copy the ID and token values as you’ll need them moving forward. Click **“Edit token permissions”** to proceed.

Be sure to copy the service token after you create it. There’s no way to retrieve the token value once you leave this page.

![Service token detail page](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/service-tokens/modal-with-service-token-2.png?w=2500&fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=9e0549863228c5adf442a450b9632190)

Service token detail page

## Assign service token permissions

Service tokens are configured with granular permissions, both for the organization that owns them as well as on a per database level. Before you can use a service token, these permissions must be added.

### Add organization permissions

Organization permissions are required when performing operations that are specific to the organization and not for an individual database. To enable a service token for performing these operations, locate the **Organization access** section and click **“Add organization permissions”**.

In the **Organization access permissions** modal, check the box next to each of the permission scopes that you want to assign to the token. Click **“Save permissions”** once finished.

For a full list of organization access permissions, see the [API documentation for service tokens](../api/reference/service-tokens.md#organization-access-permissions).

### Add database permissions

In order to perform operations specific to a database, permissions can be assigned per-database. To do this, locate the section titled **Database access** and click **“Add database access”** to open the **Database access permissions** modal.

Select the database you want to grant access to and check the box next to each permission option you need to grant. Once you are done, click **“Save permissions”**.

For a full list of database access permissions, see the [API documentation for service tokens](../api/reference/service-tokens.md#database-access-permissions).

![The Database access permissions modal.](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/service-tokens/db-access-permissions-2.png?w=2500&fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=a865a4bfd9b1cbc48b402919149855ae)

The Database access permissions modal.

### Add permissions for all databases

Organization admins can grant database permissions to all current and future databases. This is available in the **Permissions for all databases** section. Any permission added to all databases will not be able to be disabled on an individual basis.

## Service tokens and deploy requests approvals

When a database requires administrator approval for deploy requests (located in your database’s **Settings** page), a service token cannot approve a deploy request created by the same service token. Also, users can’t approve a deploy request created by a service token that they created.

## Use a service token with the PlanetScale CLI

To use service tokens with the PlanetScale CLI, set the following environment variables in your terminal:

```shellscript
export PLANETSCALE_SERVICE_TOKEN=<YOUR_SERVICE_TOKEN>
export PLANETSCALE_SERVICE_TOKEN_ID=<YOUR_SERVICE_TOKEN_ID>
```

When you execute commands using the PlanetScale CLI, it will automatically parse those values and use them to access the service. However, you’ll also need to pass in your organization name using the `--org` flag like so:

```shellscript
pscale branch create <DB_NAME> <BRANCH_NAME> --org <ORG_NAME>
```

If you don’t want to set environment variables, you may also pass in the Service Token and Service Token ID by using the [`--service-token` and `--service-token-id` flags](service-token.md) respectively:

```shellscript
pscale branch create <DB_NAME> <BRANCH_NAME> --org <ORG_NAME> --service-token <SERVICE_TOKEN> --service-token-id <SERVICE_TOKEN_ID>
```

## Use a service token with the PlanetScale API

In order to execute a request to the PlanetScale API, you’ll need a service token to execute requests directly or for minting OAuth tokens. Both the ID and token are required in the `Authorization` header without a scheme. Below is an example of how to use a service token to list details about the organizations the token can access:

```shellscript
curl --request GET \
     --url 'https://api.planetscale.com/v1/organizations' \
     --header 'Authorization: <SERVICE_TOKEN_ID>:<SERVICE_TOKEN>'
```

Refer to the [API docs](../api/reference/getting-started-with-planetscale-api.md) for more details on how to use the API.

## Modify service token permissions

If you want to modify the permissions granted to a service token, start by opening the service token from the settings pane. Select the three dots next to the organization or database name permissions you want to modify and click **“Edit permissions”**.

![The location of the Edit permissions option for organization permissions.](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/service-tokens/edit-org-perms-2.png?w=2500&fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=90cc315d328b832a8f3f1820cef1b4be)

The location of the Edit permissions option for organization permissions.

This will open a modal that allows you to modify the permissions the service token has to access that organization.

## Delete a service token

You can delete a service token at any time from the service token detail page. Simply click the **“Delete service token”** button.

![Delete service token.](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/service-tokens/delete-service-token-2.png?w=2500&fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=d7ac5491944cb607a0102bcadbd7c362)

Delete service token.

## Manage service tokens using the PlanetScale CLI

Service tokens can also be created and managed directly from the [PlanetScale CLI](service-token.md).

### Create a new service token

Use the following command to create a service token:

```shellscript
pscale service-token create
```

You can optionally specify a name for the service token using the `--name` flag:

```shellscript
pscale service-token create --name "My Token"
```

You can also specify a time to live (TTL) in seconds using the `--ttl` flag. The token will be invalid once the TTL has passed:

```shellscript
pscale service-token create --name "My Token" --ttl 3600
```

This command will return a service token ID, value, and expiration time (if TTL was specified) for your use.

### Add database access permissions

You can add database access permissions to your service token for each database in your organization.

To add database access permissions, use the command:

```shellscript
pscale service-token add-access <SERVICE_TOKEN_ID> <ACCESS_PERMISSION> --database <DB_NAME>
```

For example, to give a service token the ability to create, read, and delete branches on a specific database, use the following command:

```shellscript
pscale service-token add-access <SERVICE_TOKEN_ID> read_branch delete_branch create_branch --database <DB_NAME>
```

A complete list of service token access permissions can be found in the [PlanetScale API documentation](../api/reference/service-tokens.md#access-permissions).

### Remove database access permissions

You can also remove database access permissions for a service token.

Use the following command to remove one or more permissions:

```shellscript
pscale service-token delete-access <SERVICE_TOKEN_ID> <ACCESS_PERMISSION> --database <DB_NAME>
```

### Delete a service token

To delete a service token, run the following command:

```shellscript
pscale service-token delete <SERVICE_TOKEN_ID>
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
