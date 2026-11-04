---
url: https://planetscale.com/docs/api/reference/getting-started-with-planetscale-api
title: "Getting Started With Planetscale Api"
description: ""
access_date: 2026-11-04T23:12:49.500Z
current_date: 2026-11-04T23:12:49.500Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Getting started with PlanetScale API

> Learn how to start using the PlanetScale API.

## Overview

You can use the PlanetScale API to manage your PlanetScale databases programmatically.

The PlanetScale API does **not** include direct access to the data in the database. Some endpoints will consist of database schema information or connection information.

## Base URL

Every endpoint is under `https://api.planetscale.com/v1`. Requests and responses are JSON.

## Authentication

Before making your first API call, set up the proper authentication for the PlanetScale API using the `Authorization` header. There are two API authentication types: **Service tokens** and **OAuth**.

### Service tokens

Send a service token as a Bearer token:

```bash theme={null}
curl --request GET \
  --url 'https://api.planetscale.com/v1/organizations' \
  --header 'Authorization: Bearer <SERVICE_TOKEN>'
```

The API also accepts the token ID and token together, with no scheme: `Authorization: <SERVICE_TOKEN_ID>:<SERVICE_TOKEN>`. Older service tokens only work in this form. Sending the token on its own, without `Bearer` or the ID, returns `401`.

Most endpoints only need a service token for authentication, but some organization-specific endpoints also need OAuth. Each endpoint will state what types of authentication are allowed. See the [Service tokens documentation](https://planetscale.com/docs/api/reference/service-tokens.md) for creating a service token and granting it permissions.

### OAuth applications

All OAuth applications have a comprehensive list of scopes that the application can request from the PlanetScale user. See the [OAuth documentation](https://planetscale.com/docs/api/reference/oauth.md) for more info.

## Finding endpoints

The sidebar lists every endpoint, grouped by product. The same reference is available as one text file at `https://planetscale.com/docs/_llms/api-reference.txt`, and the OpenAPI specification is served at `https://api.planetscale.com/v1/openapi-spec`. See [OpenAPI specification](https://planetscale.com/docs/api/openapi-spec.md) for details.

## Waiting for new resources

Creating a database, branch, or backup returns before the resource is ready. Poll it until it is ready before you connect to it or use it:

* Databases: poll [Get a database](https://planetscale.com/docs/api/reference/get_database.md) until `state` is `ready`.
* Branches: poll [Get a branch](https://planetscale.com/docs/api/reference/get_branch.md) until `ready` is `true`.
* Backups: poll [Get a backup](https://planetscale.com/docs/api/reference/get_backup.md) until `state` is `success`.

Each of these usually takes a few minutes. Poll every few seconds instead of in a tight loop.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
