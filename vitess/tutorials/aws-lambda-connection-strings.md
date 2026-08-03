---
url: https://planetscale.com/docs/vitess/tutorials/aws-lambda-connection-strings
title: "Aws Lambda Connection Strings"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Introduction

We’ll use a [pre-built NodeJS](https://github.com/planetscale/aws-connection-strings-example) app for this example, but you can follow along using your own application as well.

## Prerequisites

- An AWS account
- A [PlanetScale account](https://auth.planetscale.com/sign-up)

## Set up the database

If you already have a database with a production branch, skip to [the next section](#configure-the-lambda-function).

Let’s start by creating the database. In the PlanetScale dashboard, click the “ **New database** ” button followed by “ **Create new database** ”. Name the database **lambda-connection-strings,** or any other name that you prefer. Click “ **Create database** ”.

Once your database has finished initializing, you’ll need to enable the web console on production branches. To do this, go to the “ **Settings** ” tab, check “ **Allow web console access to production branches** ”, and click “ **Save database settings** ”.

Now, access the console of the main branch by clicking “ **Console** ”, then “ **Connect** ”.

![The console](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/console-3.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=185f96980b325ff45b0b67f33ce6573d)

The console

Create a simple table & insert some data using the following script:

```sql
CREATE TABLE Tasks(
    Id int PRIMARY KEY AUTO_INCREMENT,
    Name varchar(100),
    IsDone bit
);

INSERT INTO Tasks (Name) VALUES ('Clean the kitchen');
INSERT INTO Tasks (Name) VALUES ('Fold the laundry');
INSERT INTO Tasks (Name) VALUES ('Watch the sportsball game');
```

You may run `SELECT * FROM Tasks` to ensure the data was properly added from the console.

![Records from the console](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/select.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=d185f9ce1e460a68c0ff2a47e9315136)

Records from the console

Now we need to enable [**safe migrations**](../schema-changes/safe-migrations.md) on the **main** branch. Click the **Dashboard** tab, then click the **cog** icon in the upper right of the infrastructure card.

![The option to promote a branch](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/production-2.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=fdabceb3370b50f08c6a6096d880a01d)

The option to promote a branch

Toggle on the “ **Enable safe migrations** ” option and click the “ **Enable safe migrations** ” button.

![Enable safe migrations](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/safe-migrations-2.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=6314b6f18d30142f8a3e3bc079118800)

Enable safe migrations

Before moving on from the PlanetScale dashboard, grab the connection details to be used in the next step. Click on the **Connect** button to go to the Connect page. Enter a name for your password, and click the **Create password** button to generate a new password.

![The dashboard after the database has been promoted](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/promoted-2.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=dbe239c2350359d31cc3144c4dd37cd4)

The dashboard after the database has been promoted

In the **Select your language or framework** section, select **Node.js** and note the details in the `.env` section of the guide. These details will be required to connect to the database.

## Configure the Lambda function

Secrets in AWS Lambda functions, which include database connection strings, are often stored as environment variables with the Lambda function. We’ll be uploading a sample NodeJS app that has been provided and storing the connection string from the previous section as an environment variable to test.

Start by cloning the following Git repository:

```shellscript
git clone https://github.com/planetscale/aws-connection-strings-example.git
```

Log into the AWS Console, use the universal search to search for ‘ **Lambda** ’, and select it from the list of services.

![Search for Lambda in the AWS Console](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/aws.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=0dcb50226894e97cd68257c2d47b59a2)

Search for Lambda in the AWS Console

Create a new function using the **Create function** button in the upper right of the console.

![The default view of Lambda functions](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/functions.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=1890f9e29e5595cc931a5bb5f8023e55)

The default view of Lambda functions

Name your function **lambda-connection-strings** (or any other name that suits you) and select **NodeJS** under **Runtime**. The other fields can be left as default. Click **Create function** to finish the initial setup of your Lambda.

![The view to create a Lambda function](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/create-function.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=3c757611b10f22e98f2e8f8926e46bca)

The view to create a Lambda function

On the next view, about halfway down the page you’ll see a section called **Code source**. Click the **Upload from** button, then **.zip file**.

![The default NodeJS Lambda function](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/node.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=dd86eb3da198bbfb3707a2ad1753ece1)

The default NodeJS Lambda function

Click the **Upload** button which will display a file browser. Select the **aws-connection-strings-example.zip** file from the **dist** folder of the provided repository. Click **Save** once it’s been selected.

![The modal to upload code](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/upload.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=5aafc9d790378870e934b00dfd008807)

The modal to upload code

The contents of the code editor under **Code source** should have updated to show the code stored in the zip file.

![The code of the Lambda function that was uploaded](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/source.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=c9f63a00d1ac98054d3f714e353ca982)

The code of the Lambda function that was uploaded

### Configure environment variables

Next, you need to set the PlanetScale `DATABASE_URL` environment variable that you copied earlier. Select the **Configuration** tab, and click **Edit**.

![The configuration tab](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/configuration.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=97673615ac47d36690bd3174c2692bd3)

The configuration tab

You’ll be presented with a view to add or update environment variables. Click **Add environment variable** and the view will update with a row to add an environment variable. Set the **Key** field to **DATABASE\_URL** and the **Value** to the connection string taken from the previous section. Click **Save** once finished.

![The view to manage environment variables](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/environment-variables.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=18842b5a3841cc659a1950f4958cd6b7)

The view to manage environment variables

Finally, test the function by selecting the **Test** tab, and then clicking the **Test** button.

![The test tab](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/test.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=1bb44d2711fe290a7415d4c174a557de)

The test tab

An **Execution results** box will display above the **Test event** section. If the box is green, it likely means everything executed as expected. Click the dropdown next to **Details** to see the results of the query. Since the results of the query were logged out to the console, they will be displayed in the **Log output** section.

![The execution results](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/success.png?w=2500&fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=096dfe6569fc65d025f28889bdbbd2f3)

The execution results

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
