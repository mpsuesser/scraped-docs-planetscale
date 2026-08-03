---
url: https://planetscale.com/docs/api/service-tokens
title: "Service Tokens"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

Service tokens provide an alternate authentication method to be used with the PlanetScale CLI and API. They are typically used in automated scenarios where `pscale auth login` cannot be used. Service tokens are also required for any calls to the API, as well as minting OAuth tokens for API use.

## Create service tokens using the PlanetScale dashboard

To create a service token using the dashboard, log into your organization, go to the [**“Settings”** > **“Service tokens”**](https://app.planetscale.com/~/settings/service-tokens) page, and click the **“New service token”** button.

Give the token a name (this is used for your reference only) and click **“Create service token”**.

The modal will update, displaying your service token where the Name field was. Copy the ID and token values as you’ll need them moving forward. Click **“Edit token permissions”** to proceed.

Be sure to copy the service token after you create it. There’s no way to retrieve the token value once you leave this page.

![Service token detail page](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/service-tokens/modal-with-service-token-2.png?w=2500&fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=9e0549863228c5adf442a450b9632190)

Service token detail page

## Assign service token permissions

Service tokens are configured with granular permissions, both for the organization that owns them as well as on a per database level. Before you can use a service token, these permissions must be added.

### Add organization permissions

Organization permissions are required when performing operations that are specific to the organization and not for an individual database. To enable a service token for performing these operations, locate the **Organization access** section and click **“Add organization permissions”**.

In the **Organization access permissions** modal, check the box next to each of the permission scopes that you want to assign to the token. Click **“Save permissions”** once finished.

For a full list of organization access permissions, see the [API documentation for service tokens](reference/service-tokens.md#organization-access-permissions).

### Add database permissions

In order to perform operations specific to a database, permissions can be assigned per-database. To do this, locate the section titled **Database access** and click **“Add database access”** to open the **Database access permissions** modal.

Select the database you want to grant access to and check the box next to each permission option you need to grant. Once you are done, click **“Save permissions”**.

For a full list of database access permissions, see the [API documentation for service tokens](reference/service-tokens.md#database-access-permissions).

![The Database access permissions modal.](https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/service-tokens/db-access-permissions-2.png?w=2500&fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=a865a4bfd9b1cbc48b402919149855ae)

The Database access permissions modal.

### Add permissions for all databases

Organization admins can grant database permissions to all current and future databases. This is available in the **Permissions for all databases** section. Any permission added to all databases will not be able to be disabled on an individual basis.

## Creating databases with service tokens

A service token with the `create_databases` organization permission can create new databases in your organization. When a service token creates a database, it is automatically granted [database access permissions](reference/service-tokens.md#database-access-permissions) for that database. You do not need to add database permissions separately for each database the token creates.

Only organization administrators can grant the `create_databases` permission to a service token. Auto-granted permissions apply only to databases the token creates, not to existing databases in the organization.

To grant this permission from the dashboard, open the service token’s **Organization access** section and click **“Add organization permissions”**. In the **Organization access permissions** modal, check the box for **Create organization databases** and click **“Save permissions”**.

You can also grant organization access using the CLI:

```shellscript
pscale service-token add-access <SERVICE_TOKEN_ID> create_databases
```

Once the `create_databases` permission is in place, you can create databases with the token using the CLI or API.

Refer to the [Create a database](reference/create_database.md) API reference for all available parameters.

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

If you don’t want to set environment variables, you may also pass in the Service Token and Service Token ID by using the [`--service-token` and `--service-token-id` flags](../cli/service-token.md) respectively:

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

Refer to the [API docs](reference/getting-started-with-planetscale-api.md) for more details on how to use the API.

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

Service tokens can also be created and managed directly from the [PlanetScale CLI](../cli/service-token.md).

### Create a new service token

Use the following command to create a service token:

```shellscript
pscale service-token create
```

This command will return a service token ID and value for your use.

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

A complete list of service token access permissions can be found in the [PlanetScale API documentation](reference/service-tokens.md#access-permissions).

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

## Service token automation

Running `pscale` in CI or an AI agent uses a [service token](../cli/service-tokens.md) instead of `pscale auth login`. Provide the token as environment variables (recommended) or as flags on each command; both are equivalent:

```shellscript
# Environment variables (recommended for CI and agents)
export PLANETSCALE_SERVICE_TOKEN_ID="<SERVICE_TOKEN_ID>"
export PLANETSCALE_SERVICE_TOKEN="<SERVICE_TOKEN>"

# Or per-command flags
pscale database list --org <org> \
  --service-token-id "<SERVICE_TOKEN_ID>" \
  --service-token "<SERVICE_TOKEN>"
```

A service token has **no active organization**, so resource commands need the org supplied explicitly. Set it with `PLANETSCALE_ORG`, the `--org` flag on the subcommand, or `pscale org switch <org>` (which works with a service token):

```shellscript
export PLANETSCALE_ORG="<org>"
pscale database list --format json          # uses PLANETSCALE_ORG
pscale database list --org <org> --format json   # explicit flag
```

`--org` goes on the **resource subcommand** (`database`, `branch`, `sql`, `api`, …), never on root `pscale`. `pscale --org <org> database list` fails with `unknown flag: --org`.

Per-command-family matrices (env-var auth, `--service-token` flag, required `--org`, Postgres/Vitess, `--format json`, API equivalent): [`org`](../cli/org.md#service-token-automation-org) · [`service-token`](../cli/service-token.md#service-token-automation-service-token) · [`database`](../cli/database.md#service-token-automation-database) · [`branch`](../cli/branch.md#service-token-automation-branch) · [`role`](../cli/role.md#service-token-automation-role) · [`password`](../cli/password.md#service-token-automation-password).

### Commands to avoid under a service token

Do not retry these with service-token auth. The failure is by design, not transient. This is the most common cause of agent retry loops.

| Command | Why | Do this instead | Exact error |
| --- | --- | --- | --- |
| `pscale org show` | A service token has no “current” organization | `pscale org list --format json`, then pass `--org` or `PLANETSCALE_ORG` | `not authenticated yet. Please run 'pscale auth login'` |
| `pscale service-token list` | Token management is blocked when authenticated with a token | `pscale api organizations/<org>/service-tokens --format json` | `pscale service-token list is unavailable when authenticated with a service token` |
| `pscale service-token show-access` | Same as `list` | `pscale api organizations/<org>/service-tokens/<id> --format json` | `pscale service-token show-access is unavailable when authenticated with a service token` |
| `pscale service-token` (other sub-commands) | Create/manage tokens blocked under service-token auth | `pscale auth login`, then CLI; or [Service tokens API](reference/service-tokens.md) | `pscale service-token <sub-command> is unavailable when authenticated with a service token` |
| `pscale auth login` / `logout` | Not needed; the token is the credential | Set the token env vars; run `pscale auth check --format json` to confirm | requires an interactive browser device flow |
| `pscale shell` / `connect` | Interactive sessions | Use [`pscale sql`](../cli/sql.md) for non-interactive queries | opens an interactive session |

`pscale org show` prints `not authenticated yet. Please run 'pscale auth login'` under a valid service token. This is the **no-current-org** state, not an auth failure. Do **not** respond by running `pscale auth login`. Discover the org with `pscale org list` and pass it with `--org` or `PLANETSCALE_ORG`.

`pscale auth check --format json` confirms a token is wired up. Expect `"authenticated": true`, `"auth_method": "service_token"`, and (until an org is set) an `action_required` status with a `NO_ORG` issue and a non-zero exit code. Resolve it by passing `--org` / `PLANETSCALE_ORG` on your commands.

### pscale api fallback for hard-to-parse output

When a command’s human output is hard to parse, prefer `--format json`, or drop to the raw API with `pscale api`, which returns the API response verbatim and uses the same token:

```shellscript
# org list
pscale api organizations --format json

# database list / show
pscale api organizations/<org>/databases --format json
pscale api organizations/<org>/databases/<database> --format json

# branch list / show
pscale api organizations/<org>/databases/<database>/branches --format json
pscale api organizations/<org>/databases/<database>/branches/<branch> --format json

# service-token list / show-access (CLI blocked under service-token auth)
pscale api organizations/<org>/service-tokens --format json
pscale api organizations/<org>/service-tokens/<id> --format json
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
