---
url: https://planetscale.com/docs/api/reference/getting-started-with-planetscale-api
title: "Getting Started With Planetscale Api"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Getting started with PlanetScale API

> Learn how to start using the PlanetScale API.

## Overview

You can use the PlanetScale API to manage your PlanetScale databases programmatically.

The PlanetScale API does **not** include direct access to the data in the database. Some endpoints will consist of database schema information or connection information.

## Authentication

Before making your first API call, set up the proper authentication for the PlanetScale API using the `Authorization` header. There are two API authentication types: **Service tokens** and **OAuth**.

### Service tokens

Most endpoints only need a service token for authentication, but some organization-specific endpoints also need OAuth. Each endpoint will state what types of authentication are allowed. See the [Service tokens documentation](service-tokens.md) for creating a service token and making your first API call with the PlanetScale API.

### OAuth applications

All OAuth applications have a comprehensive list of scopes that the application can request from the PlanetScale user. See the [OAuth documentation](oauth.md) for more info.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
