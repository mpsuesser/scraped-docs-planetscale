---
url: https://planetscale.com/docs/vitess/tutorials/connect-go-gorm-app
title: "Connect Go Gorm App"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Introduction

In this tutorial, you’ll learn how to connect a Go application to a PlanetScale MySQL database using a sample Go starter app with GORM.

Already have a Go application and just want to connect to PlanetScale? Check out the [Go quick connect repo](https://github.com/planetscale/connection-examples/tree/main/go).

## Prerequisites

- [Go](https://go.dev/doc/install)
- A [PlanetScale account](https://auth.planetscale.com/sign-up)
- [PlanetScale CLI](https://github.com/planetscale/cli) — You can also follow this tutorial in the PlanetScale admin dashboard, but the CLI will make setup quicker.

## Set up the Go app

This guide will integrate [a simple Go (Golang) app](https://github.com/planetscale/golang-example) with PlanetScale that will display a list of products stored in the database. If you have an existing application, you can also use that.

## Set up the database

Next, you need to set up your PlanetScale database and connect to it in the Go application.

You can create a database in the [PlanetScale dashboard](https://app.planetscale.com/) or from the PlanetScale CLI. This guide will use the CLI, but you can follow the database setup instructions in the [PlanetScale quickstart guide](planetscale-quick-start-guide.md) if you prefer the dashboard.

That’s it! Your database is ready to use. Next, let’s connect it to the Go application and then add some data.

## Connect to the Go app

There are **two ways to connect** your Go app to PlanetScale:

- With an auto-generated username and password
- Using the PlanetScale proxy with the CLI

Both options are covered below.

### Option 1: Connect with username and password (Recommended)

### Option 2: Connect with the PlanetScale proxy

To connect with the PlanetScale proxy, you need the [PlanetScale CLI](https://github.com/planetscale/cli).

## Run migrations and seeder

Now that you’re connected let’s add some data to see it in action. The sample application has an endpoint that you can use to run migrations to create your `categories` and `products` tables. It will seed your database with sample product and category data. You can find this in `main.go`.

Let’s run those now.

### Foreign key constraints

If you’re using GORM in your Go application and [do not want to use foreign key constraints](../operating-without-foreign-key-constraints.md), you can turn them off with this line in the `main.go` file of the Go starter application:

```go
// ...
DisableForeignKeyConstraintWhenMigrating: true,
// ...
```

If you prefer to use foreign key constraints in your Go application, you can skip the previous step. However, you need to first enable [foreign key constraint](../foreign-key-constraints.md) support in your database settings page.

## Add data manually

If you want to continue to play around with adding data on the fly, you have a few options:

- PlanetScale CLI shell
- PlanetScale dashboard console
- Your favorite MySQL client (for a list of tested MySQL clients, review our article on [how to connect MySQL GUI applications](connect-mysql-gui.md))

The first two options are covered below.

### Add data with PlanetScale CLI

You can use the PlanetScale CLI to open a MySQL shell to interact with your database.

You may need to install the MySQL command line client if you haven’t already.

### Add data with PlanetScale dashboard console

If you don’t care to install MySQL client or the PlanetScale CLI, another quick option is using the MySQL console built into the PlanetScale dashboard.

![PlanetScale console insert and select example](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/connect-go-gorm-app/console-2.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=0ee502a46491e24c95b4e6d9ce719c6b)

PlanetScale console insert and select example

You can now refresh the [Go products page](http://localhost:8080/products) to see the new record.

## What’s next?

Once you’re done with initial development, you can enable [safe migrations](../schema-changes/safe-migrations.md) on your `main` production branch to protect it against direct schema changes and enable zero-downtime schema migrations.

When you’re reading to make more schema changes, you’ll [create a new branch](../schema-changes/branching.md) off of your production branch. Branching your database creates an isolated copy of your production schema so that you can easily test schema changes in development. Once you’re happy with the changes, you’ll open a [deploy request](../schema-changes/deploy-requests.md). This will generate a diff showing the changes that will be deployed, making it easy for your team to review.

Learn more about how PlanetScale allows you to make [non-blocking schema changes](../schema-changes.md) to your database tables without locking or causing downtime for production databases.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
