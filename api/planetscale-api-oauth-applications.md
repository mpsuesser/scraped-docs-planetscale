---
url: https://planetscale.com/docs/api/planetscale-api-oauth-applications
title: "Planetscale Api Oauth Applications"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale API and OAuth applications

> The PlanetScale API allows you to manage your PlanetScale databases programmatically.

## PlanetScale API overview

The API supports many actions you can take in the PlanetScale web app or CLI and can be used to integrate PlanetScale into your existing workflows and tools.

The PlanetScale API does **not** include direct access to the data in the database. Some endpoints will consist of database schema information or connection information. You will still need to use a [client library, object–relational mapping tool (ORM)](../vitess/tutorials/connect-any-application.md), or the [PlanetScale serverless driver](../vitess/tutorials/planetscale-serverless-driver.md) to read from and write data to your PlanetScale database.

Learn more about the API and how to use it in the [PlanetScale API documentation](reference/getting-started-with-planetscale-api.md).

## Use cases

Some examples of what you can do with the PlanetScale API:

* Automatically [create and delete database branches](reference/create_branch.md) from CI/CD pipelines or data migration tooling
* Programmatically build out new environments that connect to PlanetScale database branches for testing
* Get information about a PlanetScale user, database, branch, organization, and deploy request
* [Check the status of deploy requests](reference/get_deploy_request.md) in the deploy queue
* Automate [creating and deleting database connection strings](reference/create_password.md) for internal users or tools
* [Create, update, approve, deploy, and delete deploy requests](reference/create_deploy_request.md) programmatically from tooling outside of PlanetScale

Anywhere that can programmatically use an HTTP API can be integrated with PlanetScale. This includes CLI tools, build scripts, desktop applications, and more.

## OAuth applications

The PlanetScale platform also allows you to create OAuth applications. Creating an OAuth application within PlanetScale allows your application to access your users’ PlanetScale accounts.

With PlanetScale OAuth applications, you can choose what access your application needs, and a user will allow (or deny) your application those accesses on their PlanetScale account. The organization that you create the OAuth application in is the "owner" of the application.

By using PlanetScale OAuth as a PlanetScale partner or developer, your organization is agreeing to prominently display a privacy policy and obtain consent to your terms of use from all users of your products and services.

You can read more about creating an OAuth application in PlanetScale in the [PlanetScale OAuth documentation](reference/oauth.md).

If you build something you would like to share with us, please email us at `education (at) planetscale.com`. We would love to hear about your experience building the application, and we may even feature your application in future blog posts, videos, or social media.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
