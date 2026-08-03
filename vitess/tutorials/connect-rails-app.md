---
url: https://planetscale.com/docs/vitess/tutorials/connect-rails-app
title: "Connect Rails App"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Introduction

In this tutorial, you’re going to create a simple Rails application named *blog* and connect it to a PlanetScale database. You’ll perform the initial migration from your local Rails application, and set up the database for future development.

Already have a Rails application and just want to connect to PlanetScale? Check out the [Rails quick connect repo](https://github.com/planetscale/connection-examples/tree/main/ruby).

## Prerequisites

- Install [Ruby and the Rails gem](https://guides.rubyonrails.org/getting_started.html#creating-a-new-rails-project-installing-rails).
- Install the [PlanetScale CLI](https://github.com/planetscale/cli).
- Authenticate the CLI with the following command:

```shellscript
pscale auth login
```

## Create a Rails project

To connect a Rails application to a PlanetScale database, you’ll first create a sample Rails project named *blog* and install the libraries needed to connect to your PlanetScale database.

Open the command line and follow these steps:

## Create a PlanetScale database and password

Now you’ll need to create credentials for your Rails application to use.

### Using the CLI to create a connection string

You can also create passwords in the PlanetScale dashboard, as documented [in our Creating a password documentation](../connecting/connection-strings.md#creating-a-password).

## Configure Rails and PlanetScale

Let’s set up the Rails application to talk to the new database.

Open `config/database.yml` and configure the `development` database settings with your new credentials from the output in the previous step:

```yaml
development:
  <<: *default
  adapter: trilogy
  database: blog
  username: <USERNAME>
  host: <ACCESS HOST URL>
  password: <PASSWORD>
  ssl_mode: <%= Trilogy::SSL_VERIFY_IDENTITY %>
```

For `database` (database name), you can use your PlanetScale database name directly if you have a *single unsharded keyspace*. If you have a sharded keyspace, you’ll need to use `@primary`. This will automatically direct incoming queries to the correct keyspace/shard. For more information, see the [Targeting the correct keyspace documentation](../sharding/targeting-correct-keyspace.md).

The correct `sslca` path depends on your operating system and distribution. See [CA root configuration](../connecting/secure-connections.md#ca-root-configuration) for more information.

You’re configuring the **development** Rails environment here for the sake of expedience. In actual use, the **main** database branch would typically serve the **production** environment.

Because this is a Rails app, you can also enable [Automatic Rails migrations](automatic-rails-migrations.md) from the database’s settings page. Select your database, click on the `main` branch, click “ **Settings** ”, check the “ **Automatically copy migration data** ” box, and select “ **Rails** ” from the dropdown.

## Migrate your database

Here comes the fun stuff! Now that your application is configured to talk to PlanetScale, you can create your first migration.

## Enable safe migrations

[Safe migrations](../schema-changes/safe-migrations.md) is an optional but highly recommended feature for branches on PlanetScale. With safe migrations enabled, direct schema changes (`CREATE`, `ALTER`, and `DELETE`) are not allowed on production branches to prevent accidental data loss and must be applied via [deploy requests](../best-practices.md).

```shellscript
pscale branch safe-migrations enable blog main
```

Congratulations! You’re ready to develop your Rails application against PlanetScale.

## Summary

In this tutorial, you created a simple Rails application named *blog* and connected it to a PlanetScale database.

## Further reading

If you’re interested in learning how to secure your application’s connection to PlanetScale, please read [Connecting to PlanetScale securely](../connecting/secure-connections.md).

## What’s next?

Now that you’ve successfully connected your Rails app to PlanetScale, it’s time to make more schema changes to your tables! Learn more about how PlanetScale allows you to make [non-blocking schema changes](../schema-changes.md) to your database, or how to keep your **schema\_migrations** table up-to-date between development and production branches with [automatic schema migrations](automatic-rails-migrations.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
