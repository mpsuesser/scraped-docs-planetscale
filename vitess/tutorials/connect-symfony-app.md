---
url: https://planetscale.com/docs/vitess/tutorials/connect-symfony-app
title: "Connect Symfony App"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

## Prerequisites

- [PHP](https://www.php.net/manual/en/install.php) — This tutorial uses `v8.1`
- [Composer](https://getcomposer.org/)
- A [PlanetScale account](https://auth.planetscale.com/sign-up)
- [PlanetScale CLI](https://github.com/planetscale/cli) — You can also follow this tutorial using just the PlanetScale admin dashboard, but the CLI will make setup quicker.

## Set up the Symfony app

This guide will integrate [a simple Symfony app](https://github.com/planetscale/symfony-example) with PlanetScale that will display a list of products stored in the database. If you have an existing application, you can also use that.

You can view the application at [http://localhost:8000](http://localhost:8000/).

## Set up the database

Next, you need to set up your PlanetScale database and connect to it in the Symfony application.

You can create a database either in the [PlanetScale dashboard](https://app.planetscale.com/) or from the PlanetScale CLI. This guide will use the CLI, but you can follow the database setup instructions in the [PlanetScale quickstart guide](planetscale-quick-start-guide.md) if you prefer the dashboard.

This tutorial uses `symfony_example` for `DATABASE_NAME`, but you can use any name with lowercase, alphanumeric characters, or underscores. You can also use dashes, but we don’t recommend them, as they may need to be escaped in some instances.

For `REGION_SLUG`, choose a region closest to you from the [available regions](https://planetscale.com/docs/vitess/regions#available-regions) or leave it blank.

That’s it! Your database is ready to use. Next, let’s connect it to the Symfony application and then add some data.

## Connect to the Symfony app

There are **two ways to connect** to PlanetScale:

- With an auto-generated username and password
- Using the PlanetScale proxy with the CLI

Both options are covered below.

### Option 1: Connect with username and password (Recommended)

These instructions show you how to use the [PlanetScale CLI](../../cli/planetscale-environment-setup.md) to generate a set of credentials.

You can also get these exact values to to copy/paste from your [PlanetScale dashboard](https://app.planetscale.com/). In the dashboard, click on the database > “ **Connect** ” > “ **Generate new password** ” > “ **General** ” dropdown > “ **Symfony** ”. If the password is blurred, click “ **New password** ”. Skip to step 3 once you have these credentials.

Refresh your Symfony homepage and you should see the message that you’re connected to your database!

### Option 2: Connect with the PlanetScale proxy

To connect with the PlanetScale proxy, you’ll need the [PlanetScale CLI](https://github.com/planetscale/cli).

Delete that line and save.

Refresh your Symfony homepage and you should see the message that you’re connected to your database!

## Run migrations and seeder

Now that you’re connected, let’s add some data to see it in action. The sample application comes with a migration file at `migrations/Version20220120102247.php` that will create `category` and `product` tables in the database.

There are also two seeder files, `src/DataFixtures/CategoryFixtures.php` and `src/DataFixtures/ProductFixtures.php`, that will add ten random categories and products to the `category` and `product` tables, respectively. Let’s run those now.

The `templates/product/index.html.twig` file pulls this data from the `product` table with the help of the `src/Controller/ProductController.php` file.

![Symfony PlanetScale starter app homepage](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/connect-symfony-app/example.png?w=2500&fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=9a77f6d01359a94f1f4604b5a75ea846)

Symfony PlanetScale starter app homepage

## Add data manually

If you want to continue to play around with adding data on the fly, you have a few options:

- PlanetScale CLI shell
- PlanetScale dashboard console
- Your favorite MySQL client (for a list of tested MySQL clients, review our article on [how to connect MySQL GUI applications](connect-mysql-gui.md))

The first two options are covered below.

### Add data with PlanetScale CLI

You can use the PlanetScale CLI to open a MySQL shell to interact with your database.

You may need to [install the MySQL command line client](../../cli/planetscale-environment-setup.md) if you haven’t already.

Run the following command in your terminal:

```shellscript
pscale shell <DATABASE_NAME> <BRANCH_NAME>
```

This will open up a MySQL shell connected to the specified database and branch.

A branch, `main`, was automatically created when you created your database, so you can use that for `BRANCH_NAME`.

Add a record to the `product` table:

```sql
INSERT INTO \`store_product\` (name, description, image, category_id)
VALUES  ('Spaceship', 'Get ready for the trip of a lifetime', 'https://via.placeholder.com/150.png', 2);
```

The value `id` will be filled with a default value.

You can verify it was added in the PlanetScale CLI MySQL shell with:

```sql
select * from product;
```

Type `exit` to exit the shell.

You can now refresh the [Symfony homepage](http://localhost:8000/) to see the new record.

### Add data with PlanetScale dashboard console

If you don’t care to install MySQL client or the PlanetScale CLI, another quick option using the MySQL console built into the PlanetScale dashboard.

By default, web console access to production branches is disabled to prevent accidental deletion. From your database’s dashboard page, click on the “ **Settings** ” tab, check the box labelled “ **Allow web console access to production branches** ”, and click “ **Save database settings** ”.

You can now refresh the [Symfony homepage](http://localhost:8000/) to see the new record.

## What’s next?

Once you’re done with initial development, you can enable [safe migrations](../schema-changes/safe-migrations.md) on your `main` production branch to protect it against direct schema changes and enable zero-downtime schema migrations.

Learn more about how PlanetScale allows you to make [non-blocking schema changes](../schema-changes.md) to your database tables without locking or causing downtime for production databases.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
