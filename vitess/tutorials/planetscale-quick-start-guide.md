---
url: https://planetscale.com/docs/vitess/tutorials/planetscale-quick-start-guide
title: "Planetscale Quick Start Guide"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

PlanetScale supports both Vitess and [Postgres](../../postgres/tutorials/planetscale-postgres-quickstart.md) databases. This guide covers getting started with a Vitess database.

## Overview

The following guide will show you how to:

- Create a database with PlanetScale
- Make a schema change
- Insert data
- Promote your database branch to production

If you already have your PlanetScale database set up, you may find the [Connecting your application](connect-any-application.md) or [Branching](../schema-changes/branching.md) guides more helpful.

This guide is split up so that you can either follow it in the [PlanetScale dashboard](#getting-started-%E2%80%94-planetscale-dashboard) or using the [PlanetScale CLI](#getting-started-%E2%80%94-planetscale-cli).

## Getting started — PlanetScale dashboard

You’ll need [a PlanetScale account](https://auth.planetscale.com/sign-up) to complete this guide.

### Create a database

Follow these steps to create a database:

Your database is created with a [**default production branch**](../schema-changes/branching.md), `main`, which you will use to apply a schema change and insert data. While this is just the first branch we create for you, you can always create new development branches (isolated copies of the production schema) off of production to use for development.

### Add a schema to your database

This quickstart demonstrates how to create and use two relational tables: `categories` and `products`.

### Insert data into your database

Now that you have created your tables, let’s insert some data. Run the following commands to add a product and category to your tables:

```sql
INSERT INTO categories (name)
VALUES  ('Office supplies');
```

```sql
INSERT INTO products (name, image_url, category_id)
VALUES  ('Ballpoint pen', 'https://example.com/500x500', '1');
```

You can confirm the data has been added with:

```sql
SELECT * FROM products;
```

```sql
SELECT * FROM categories;
```

You can view the schema of your database by navigating to the “ **Branches** ” tab and selecting the database you want to view. For now, select the `main` database, and it will display the names of the two tables you just created. Click on the name of each table to see further schema details.

### Enable safe migrations on your main branch

All of the work you’ve done so far has been on a default production branch, `main`, that was automatically created when you created the database.

A production branch is a highly available database branch that includes an additional replica. It also has the option to enable [safe migrations](../schema-changes/safe-migrations.md), which enables non-blocking schema changes and can protect your database from accidental schema changes.

[Safe migrations](../schema-changes/safe-migrations.md) is an optional, but highly recommended, feature that adds an additional layer of protection to your branch by preventing accidental schema modifications and enabling no-downtime schema changes. With safe migrations enabled, any DDL issued directly to the branch will not be accepted. Instead, changes must be made using the PlanetScale flow, where deploy requests are used to safely merge changes in a collaborative environment.

### To enable safe migrations:

The `main` branch now contains the `categories` and `products` tables you created, along with the data you inserted. In addition, it is highly available with an additional replica, and is enabled for zero-downtime migrations with [safe migrations](../schema-changes/safe-migrations.md).

### What’s next?

Now that you’ve created a database, applied schema changes, added data, and enable safe migrations, it’s time to connect to your application.

You can use our [Connect Any Application tutorial](connect-any-application.md) for a general step-by-step approach, one of our language-specific guides, or head straight to our [Connection Strings documentation](../connecting/connection-strings.md) for more information about creating connection strings.

When you want to continue development on your database:

When you branch off of a production branch, your development branch will have the same schema as production, but it **will not** copy over any data from the production database. We suggest seeding development branches with mock data.

## Getting started — PlanetScale CLI

Make sure you first have [downloaded and installed the PlanetScale CLI](https://github.com/planetscale/cli#installation).

You will also need a PlanetScale account. You can [sign up for a PlanetScale account here](https://auth.planetscale.com/sign-up) or run `pscale signup` to create an account straight from the CLI.

### Sign in to your account

To authenticate with the PlanetScale CLI, enter the following:

```shellscript
pscale auth login
```

You’ll be taken to a screen in the browser where you’ll be asked to confirm the code displayed in your terminal. If the confirmation codes match, click the “ **Confirm code** ” button in your browser.

You should receive the message “Successfully logged in” in your terminal. You can now close the confirmation page in the browser and proceed in the terminal.

### Create a database

Run the following command to create a database:

```shellscript
pscale database create <DATABASE_NAME> --region <REGION_SLUG>
```

- **DATABASE\_NAME** — Your database name can contain lowercase, alphanumeric characters, or underscores. We allow dashes, but don’t recommend them, as they may need to be escaped in some instances.
- **REGION\_SLUG** — For the lowest latency, choose the region closest to you or your application’s hosting location. You can find our regions and their slugs on the [Regions page](https://planetscale.com/docs/vitess/regions#available-regions).

If you do not specify a region, your database will automatically be deployed to **US East — Northern Virginia**.

Your database is created with an [**initial branch**](../schema-changes/branching.md), `main`, which you will use to apply a schema change and insert data. While this is just the first branch we create for you, you can always create new branches (isolated copies of the production schema) off of production to use for development.

### Add a schema to your database

To add a schema to your database, you will need to connect to MySQL, so [make sure you `mysql-client` installed](../../cli/planetscale-environment-setup.md#setup-overview).

### Insert data into your database

Now that you have your schema set up, let’s insert some data.

### Enable safe migrations

All of the work you’ve done so far has been on a default production branch, `main`, that was automatically created when you created the database.

A production branch is a highly available database branch that includes an additional replica. It also has the option to enable [safe migrations](../schema-changes/safe-migrations.md), which enables non-blocking schema changes and can protect your database from accidental schema changes.

[Safe migrations](../schema-changes/safe-migrations.md) is an optional, but highly recommended, feature that adds an additional layer of protection to your branch by preventing accidental schema modifications and enabling no-downtime schema changes. With safe migrations enabled, any DDL issued directly to the branch will not be accepted. Instead, changes must be made using the PlanetScale flow, where deploy requests are used to safely merge changes in a collaborative environment.

To enable safe migrations on your branch, run:

```shellscript
pscale branch safe-migrations enable <DATABASE_NAME> main
```

The `main` branch now contains the `categories` and `products` tables you created, along with the data you inserted. In addition, it is highly available with an additional replica, and is enabled for zero-downtime migrations with [safe migrations](../schema-changes/safe-migrations.md).

### What’s next?

Now that you’ve created a database, applied schema changes, added data, and enabled safe migrations, it’s time to connect to your application.

You can use our [Connect Any Application tutorial](connect-any-application.md) for a general step-by-step approach, one of our language-specific guides, or head straight to our [Connection Strings documentation](../connecting/connection-strings.md) for more information about creating connection strings.

When you want to continue development on your database:

When you branch off of a production branch, your development branch will have the same schema as production, but it **will not** copy over any data from the production database. We suggest seeding development branches with mock data.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
