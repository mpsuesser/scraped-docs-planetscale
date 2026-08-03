---
url: https://planetscale.com/docs/vitess/tutorials/connect-php-app
title: "Connect Php App"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

Already have a PHP application and just want to connect to PlanetScale? Check out the [PHP quick connect repo](https://github.com/planetscale/connection-examples/tree/main/php).

## Prerequisites

- [PHP](https://www.php.net/manual/en/install.php) — This tutorial uses `v8.1`
- [Composer](https://getcomposer.org/)
- A [PlanetScale account](https://auth.planetscale.com/sign-up)
- [PlanetScale CLI](https://github.com/planetscale/cli) (Optional) — You can also follow this tutorial using just the PlanetScale admin dashboard, but the CLI will make setup quicker.

## Set up the PHP app

This guide uses [a simple PHP app](https://github.com/planetscale/php-example) that displays a list of products stored in a PlanetScale database. If you have an existing application, you can also use that.

![PHP sample application homepage priority](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/connect-php-app/example.png?w=2500&fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=94cee531fd28f43de26cbe8d2ea35cc0)

PHP sample application homepage priority

You can view the application at [http://localhost:8000](http://localhost:8000/).

## Set up the database

Next, you need to set up your PlanetScale database and connect it to the PHP application.

You can create a database either in the [PlanetScale dashboard](https://app.planetscale.com/) or from the PlanetScale CLI.

This guide will use the CLI, but you can follow the database setup instructions in the [PlanetScale quickstart guide](planetscale-quick-start-guide.md#create-a-database) if you prefer the dashboard. Just create the database and then come back here to continue.

You can use any name with lowercase, alphanumeric characters, or underscores. You can also use dashes, but we don’t recommend them, as they may need to be escaped in some instances.

For `REGION_SLUG`, choose a region closest to you from the [available regions](https://planetscale.com/docs/vitess/regions#available-regions) or leave it blank.

Your database is created with a default branch, `main`, which is meant to serve as your production database branch.

That’s it! Your database is ready to use. Next, let’s connect it to the PHP application and then add some data.

## Connect to the PHP app

There are **two ways to connect** to PlanetScale:

- With an auto-generated username and password
- Using the PlanetScale proxy with the CLI

Both options are covered below.

The environment variables you fill in next will be used in the [`db.php` file of the sample application](https://github.com/planetscale/php-example/blob/main/db.php):

```php
<?php
$hostname = $_ENV['HOST'];
$dbName = $_ENV['DATABASE'];
$username = $_ENV['USERNAME'];
$password = $_ENV['PASSWORD'];
$ssl = $_ENV['MYSQL_ATTR_SSL_CA'];
$port = 3306;

$mysqli = mysqli_init();
$mysqli->ssl_set(NULL, NULL, $ssl, NULL, NULL);
$mysqli->real_connect($hostname, $username, $password, $dbName, $port);

if ($mysqli->connect_error) {
    echo 'not connected to the database';
} else {
    echo "Connected successfully";
}
```

For `dbName`, you can use your PlanetScale database name directly if you have a *single unsharded keyspace*. If you have a sharded keyspace, you’ll need to use `@primary`. This will automatically direct incoming queries to the correct keyspace/shard. For more information, see the [Targeting the correct keyspace documentation](../sharding/targeting-correct-keyspace.md).

### Option 1: Connect with username and password (Recommended)

If you’re not using the CLI, you can get the exact values to copy/paste from your PlanetScale dashboard. In the dashboard, select the branch you want to connect to from the infrastructure card (we’re using `main`), click “ **Connect** ”, and select “ **PHP** ” from the language dropdown. Copy these credentials, and then skip to step 2 to fill them in.

### Option 2: Connect with the PlanetScale proxy

We recommend connecting with a username and password, but you can also open a quick connection with the PlanetScale proxy. You’ll need the [PlanetScale CLI](https://github.com/planetscale/cli) for this option.

## Add the schema and data

Now that you’re connected to the database let’s create the `products` and `categories` tables and add some data. There are a few ways to do this:

- PlanetScale CLI shell
- PlanetScale dashboard console
- Your favorite MySQL client (for a list of tested MySQL clients, review our article on [how to connect MySQL GUI applications](connect-mysql-gui.md))

The first two options are covered below.

### Option 1: Add data with PlanetScale dashboard console

If you don’t care to install the MySQL client or the PlanetScale CLI, another quick option is using the MySQL console built into the PlanetScale dashboard.

You can now refresh the [PHP homepage](http://localhost:8000/) to see the new record.

### Option 2: Add data with PlanetScale CLI

You can use the PlanetScale CLI to open a MySQL shell to interact with your database.

You may need to [install the MySQL command line client](../../cli/planetscale-environment-setup.md) if you haven’t already.

You can now refresh the [PHP homepage](http://localhost:8000/) to see the new records.

## What’s next?

Once you’re done with initial development, you can promote your branch to production and enable [safe migrations](../schema-changes/safe-migrations.md) on your `main` production branch to protect it against direct schema changes and enable zero-downtime schema migraions.

When you’re reading to make more schema changes, you’ll [create a new branch](../schema-changes/branching.md) off of your production branch. Branching your database creates an isolated copy of your production schema so that you can easily test schema changes in development. Once you’re happy with the changes, you’ll [open a deploy request](../schema-changes/deploy-requests.md). This will generate a diff showing the changes that will be deployed, making it easy for your team to review.

Learn more about how PlanetScale allows you to make [non-blocking schema changes](../schema-changes.md) to your database tables without locking or causing downtime for production databases.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
