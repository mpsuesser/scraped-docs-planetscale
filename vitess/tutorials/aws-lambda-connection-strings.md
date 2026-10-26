---
url: https://planetscale.com/docs/vitess/tutorials/aws-lambda-connection-strings
title: "Aws Lambda Connection Strings"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# AWS Lambda connection strings

> In this guide, you'll learn how to properly store and use PlanetScale MySQL connection strings for use in AWS Lambda Functions.

This page covers Vitess.

## Introduction

We'll use a [pre-built NodeJS](https://github.com/planetscale/aws-connection-strings-example) app for this example, but you can follow along using your own application as well.

## Prerequisites

* An AWS account
* A [PlanetScale account](https://auth.planetscale.com/sign-up)

## Set up the database

> [!NOTE]
>
> If you already have a database with a production branch, skip to [the next section](#configure-the-lambda-function).

Let's start by creating the database. In the PlanetScale dashboard, click the "**New database**" button followed by "**Create new database**". Name the database **lambda-connection-strings,** or any other name that you prefer. Click "**Create database**".

Once your database has finished initializing, you'll need to enable the web console on production branches. To do this, go to the "**Settings**" tab, check "**Allow web console access to production branches**", and click "**Save database settings**".

Now, access the console of the main branch by clicking "**Console**", then "**Connect**".

![The console](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/console-3.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=8255d9deaa6855225cb9a041be6c5a89)

Create a simple table & insert some data using the following script:

```sql theme={null}
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

![Records from the console](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/select.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=d48ac40df1cfbb67394c9b55937c8f31)

Now we need to enable [**safe migrations**](https://planetscale.com/docs/vitess/schema-changes/safe-migrations.md) on the **main** branch. Click the **Dashboard** tab, then click the **cog** icon in the upper right of the infrastructure card.

![The option to promote a branch](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/production-2.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=3eac808438c88fbc3b87fc273742d818)

Toggle on the "**Enable safe migrations**" option and click the "**Enable safe migrations**" button.

![Enable safe migrations](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/safe-migrations-2.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=e85f41d66261fe91d36a6d081456cbec)

Before moving on from the PlanetScale dashboard, grab the connection details to be used in the next step. Click on the **Connect** button to go to the Connect page. Enter a name for your password, and click the **Create password** button to generate a new password.

![The dashboard after the database has been promoted](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/promoted-2.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=9761608fc3826672a130048a7a232274)

In the **Select your language or framework** section, select **Node.js** and note the details in the `.env` section of the guide. These details will be required to connect to the database.

## Configure the Lambda function

Secrets in AWS Lambda functions, which include database connection strings, are often stored as environment variables with the Lambda function. We’ll be uploading a sample NodeJS app that has been provided and storing the connection string from the previous section as an environment variable to test.

Start by cloning the following Git repository:

```bash theme={null}
git clone https://github.com/planetscale/aws-connection-strings-example.git
```

Log into the AWS Console, use the universal search to search for ‘**Lambda**’, and select it from the list of services.

![Search for Lambda in the AWS Console](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/aws.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=1b04f3e1c89e89f739ea09a30f17bde8)

Create a new function using the **Create function** button in the upper right of the console.

![The default view of Lambda functions](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/functions.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=04926ae1d4bc634b5d0f12fc58877fb0)

Name your function **lambda-connection-strings** (or any other name that suits you) and select **NodeJS** under **Runtime**. The other fields can be left as default. Click **Create function** to finish the initial setup of your Lambda.

![The view to create a Lambda function](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/create-function.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=8f1b1d8d1416735fac6c69ba6de610ec)

On the next view, about halfway down the page you’ll see a section called **Code source**. Click the **Upload from** button, then **.zip file**.

![The default NodeJS Lambda function](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/node.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=c4cb6bcaaaa6e2088f882c1303a5376f)

Click the **Upload** button which will display a file browser. Select the **aws-connection-strings-example.zip** file from the **dist** folder of the provided repository. Click **Save** once it’s been selected.

![The modal to upload code](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/upload.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=0b71f632247356949c022b826bb2ef84)

The contents of the code editor under **Code source** should have updated to show the code stored in the zip file.

![The code of the Lambda function that was uploaded](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/source.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=b0e9c090ece970d4bd110b82a0420ca9)

### Configure environment variables

Next, you need to set the PlanetScale `DATABASE_URL` environment variable that you copied earlier. Select the **Configuration** tab, and click **Edit**.

![The configuration tab](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/configuration.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=27df1ca8b7351d0dcf49f7da946f70fe)

You’ll be presented with a view to add or update environment variables. Click **Add environment variable** and the view will update with a row to add an environment variable. Set the **Key** field to **DATABASE\_URL** and the **Value** to the connection string taken from the previous section. Click **Save** once finished.

![The view to manage environment variables](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/environment-variables.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=faaf49570dc93d29ef979d9ee04053ab)

Finally, test the function by selecting the **Test** tab, and then clicking the **Test** button.

![The test tab](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/test.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=395b8104f5c86c154d0b6ae5ef3c08f6)

An **Execution results** box will display above the **Test event** section. If the box is green, it likely means everything executed as expected. Click the dropdown next to **Details** to see the results of the query. Since the results of the query were logged out to the console, they will be displayed in the **Log output** section.

![The execution results](https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/success.png?fit=max\&auto=format\&n=Lta43VIYjNTnQ47e\&q=85\&s=e2bc56e236240c8ec2faf45ae5db578d)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
