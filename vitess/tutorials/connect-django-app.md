---
url: https://planetscale.com/docs/vitess/tutorials/connect-django-app
title: "Connect Django App"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Introduction

In this tutorial, you’ll learn how to connect a Django application to a PlanetScale MySQL database using a pre-built Django application.

Already have a Django application and just want to connect to PlanetScale? Check out the [Django quick connect repo](https://github.com/planetscale/connection-examples/tree/main/python).

## Prerequisites

- Python — This tutorial uses `v3.6`
- A [PlanetScale account](https://auth.planetscale.com/sign-up)
- (Optional) [PlanetScale CLI](https://github.com/planetscale/cli) — This isn’t required, but it can make setup much faster

## Set up the Django application

This guide integrates a simple Django application with PlanetScale. The application has one endpoint that displays a list of products and categories pulled from the database. If you have an existing application, you can also use that.

## Set up the database

Next, you need to set up your PlanetScale database and connect to it in the Django application.

You can create a database either in the [PlanetScale dashboard](https://app.planetscale.com/) or from the PlanetScale CLI. This guide will use the CLI, but you can follow the database setup instructions in the [PlanetScale quickstart guide](planetscale-quick-start-guide.md#getting-started-%E2%80%94-planetscale-dashboard) if you prefer the dashboard.

Authenticate the CLI with the following command:

```shellscript
pscale auth login
```

Create a new database with a default `main` branch with the following command:

```shellscript
pscale database create <DATABASE_NAME> --region <REGION_SLUG>
```

This tutorial uses `django_example` for `DATABASE_NAME`, but you can use any name with lowercase, alphanumeric characters, or underscores. You can also use dashes, but we don’t recommend them, as they may need to be escaped in some instances.

For `REGION_SLUG`, choose a region closest to you from the [available regions](https://planetscale.com/docs/vitess/regions#available-regions) or leave it blank.

That’s it! Your database is ready to use. Next, let’s connect it to the Django application and then add some data.

## Connect to the Django application

There are **two ways to connect** your Django application to PlanetScale:

- With an auto-generated username and password
- Using the PlanetScale proxy with the CLI

Both options are covered below.

### Option 1: Connect with username and password (Recommended)

### Option 2: Connect with PlanetScale proxy

To connect with the PlanetScale proxy, you’ll need the [PlanetScale CLI](https://github.com/planetscale/cli).

## Optional — Bring in PlanetScale custom database wrapper

This next step is only necessary if you’re using your own application to go through this guide **and** [do not want to use foreign key constraints](../operating-without-foreign-key-constraints.md). If you cloned the sample app, this already exists in the repo.

Foreign key constraint support is not enabled by default in PlanetScale. If you’d like to use foreign key constraints in your Django application with PlanetScale, you must first enable foreign key constraint support in your database settings page.

Django uses foreign key constraint syntax by default. So, if you want to proceed without foreign key constraints, you need to pull in the PlanetScale database wrapper for Django to disable foreign key syntax in the Django migrations.

## Run migrations and seeder

Now that you’re connected, let’s add some data to see everything in action.

You can find the migrations file in `mysite/store/migrations/0002_auto_20220919_0058.py` that references the `Category` and `Product` models to create the schema. It also contains seed data to create two products and two categories. Run this migration with:

```shellscript
python manage.py migrate
```

This will also run the default Django migrations.

## Display the data

Finally, let’s display the data to confirm that everything worked correctly.

This Django starter application has a pre-built endpoint, `/products`, that will grab and display all of the product data.

To view the data, start the server:

```shellscript
python manage.py runserver
```

Then go to [`localhost:8000/products`](http://localhost:8000/products) and you’ll see a list of the data from the products table.

## Add data manually

If you want to continue playing around with adding data on the fly, you have a few options:

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

Add a record to the `store_product` table:

```sql
INSERT INTO \`store_product\` (name, description, image, category_id)
VALUES  ('Product 3', 'Product 3 description', 'https://via.placeholder.com/300.png?text=Product1', 1);
```

The value `id` will be filled with a default value.

Type `exit` to exit the shell.

Refresh the Django homepage to see the new record. You can also verify it was added in the PlanetScale CLI MySQL shell with:

```sql
select * from store_product;
```

### Add data with PlanetScale dashboard console

If you don’t care to install MySQL client or the PlanetScale CLI, another quick option using the MySQL console built into the PlanetScale dashboard.

You can also head to the [`/products`](http://localhost:8000/products) endpoint in your Django application to see the new data.

## Foreign key constraints with PlanetScale

If you’d like to use foreign key constraints in your Django application with PlanetScale, you must first enable foreign key constraint support in your database settings page.

If you prefer to enforce referential integrity at the application level, you will have to do some extra configuration with Django. You can disable foreign key constraint checks at the model level in Django, but if you’re running the default migrations, you’ll need to turn them off globally.

The [PlanetScale custom database backend](https://github.com/planetscale/django_psdb_engine) manages this, but if you want to do it manually in each model. For example, in the `models.py` file for the example in this document, we define the foreign key on the `category` table with the following:

```python
category = models.ForeignKey(Category, on_delete=models.DO_NOTHING, db_constraint=False)
```

This isn’t necessary to do in every model if you’re pulling in the `django_psdb_engine` because it overrides the setting globally anyway, but this will work if you want to do it per model.

## What’s next?

Once you’re done with initial development, you can enable [safe migrations](../schema-changes/safe-migrations.md) to protect from accidental schema changes and enable zero-downtime deployments.

Learn more about how PlanetScale allows you to make [non-blocking schema changes](../schema-changes.md) to your database tables without locking or causing downtime for production databases.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
