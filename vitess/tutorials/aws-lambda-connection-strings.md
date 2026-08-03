---
url: https://planetscale.com/docs/vitess/tutorials/aws-lambda-connection-strings
title: "Aws Lambda Connection Strings"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# AWS Lambda connection strings

> In this guide, you'll learn how to properly store and use PlanetScale MySQL connection strings for use in AWS Lambda Functions.

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

<PlatformAvailability current="vitess" />

## Introduction

We'll use a [pre-built NodeJS](https://github.com/planetscale/aws-connection-strings-example) app for this example, but you can follow along using your own application as well.

## Prerequisites

* An AWS account
* A [PlanetScale account](https://auth.planetscale.com/sign-up)

## Set up the database

<Note>
  If you already have a database with a production branch, skip to [the next section](#configure-the-lambda-function).
</Note>

Let's start by creating the database. In the PlanetScale dashboard, click the "**New database**" button followed by "**Create new database**". Name the database **lambda-connection-strings,** or any other name that you prefer. Click "**Create database**".

Once your database has finished initializing, you'll need to enable the web console on production branches. To do this, go to the "**Settings**" tab, check "**Allow web console access to production branches**", and click "**Save database settings**".

Now, access the console of the main branch by clicking "**Console**", then "**Connect**".

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/console-3.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=8255d9deaa6855225cb9a041be6c5a89" alt="The console" width="1512" height="1105" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/console-3.png" />
</Frame>

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

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/select.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=d48ac40df1cfbb67394c9b55937c8f31" alt="Records from the console" width="1262" height="331" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/select.png" />
</Frame>

Now we need to enable [**safe migrations**](../schema-changes/safe-migrations.md) on the **main** branch. Click the **Dashboard** tab, then click the **cog** icon in the upper right of the infrastructure card.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/production-2.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=3eac808438c88fbc3b87fc273742d818" alt="The option to promote a branch" width="1653" height="671" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/production-2.png" />
</Frame>

Toggle on the "**Enable safe migrations**" option and click the "**Enable safe migrations**" button.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/safe-migrations-2.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=e85f41d66261fe91d36a6d081456cbec" alt="Enable safe migrations" width="943" height="781" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/safe-migrations-2.png" />
</Frame>

Before moving on from the PlanetScale dashboard, grab the connection details to be used in the next step. Click on the **Connect** button to go to the Connect page. Enter a name for your password, and click the **Create password** button to generate a new password.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/promoted-2.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=9761608fc3826672a130048a7a232274" alt="The dashboard after the database has been promoted" width="1627" height="809" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/promoted-2.png" />
</Frame>

In the **Select your language or framework** section, select **Node.js** and note the details in the `.env` section of the guide. These details will be required to connect to the database.

## Configure the Lambda function

Secrets in AWS Lambda functions, which include database connection strings, are often stored as environment variables with the Lambda function. We’ll be uploading a sample NodeJS app that has been provided and storing the connection string from the previous section as an environment variable to test.

Start by cloning the following Git repository:

```bash theme={null}
git clone https://github.com/planetscale/aws-connection-strings-example.git
```

Log into the AWS Console, use the universal search to search for ‘**Lambda**’, and select it from the list of services.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/aws.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=1b04f3e1c89e89f739ea09a30f17bde8" alt="Search for Lambda in the AWS Console" width="1111" height="492" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/aws.png" />
</Frame>

Create a new function using the **Create function** button in the upper right of the console.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/functions.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=04926ae1d4bc634b5d0f12fc58877fb0" alt="The default view of Lambda functions" width="1850" height="659" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/functions.png" />
</Frame>

Name your function **lambda-connection-strings** (or any other name that suits you) and select **NodeJS** under **Runtime**. The other fields can be left as default. Click **Create function** to finish the initial setup of your Lambda.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/create-function.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=8f1b1d8d1416735fac6c69ba6de610ec" alt="The view to create a Lambda function" width="1286" height="920" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/create-function.png" />
</Frame>

On the next view, about halfway down the page you’ll see a section called **Code source**. Click the **Upload from** button, then **.zip file**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/node.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=c4cb6bcaaaa6e2088f882c1303a5376f" alt="The default NodeJS Lambda function" width="1272" height="1023" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/node.png" />
</Frame>

Click the **Upload** button which will display a file browser. Select the **aws-connection-strings-example.zip** file from the **dist** folder of the provided repository. Click **Save** once it’s been selected.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/upload.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=0b71f632247356949c022b826bb2ef84" alt="The modal to upload code" width="825" height="275" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/upload.png" />
</Frame>

The contents of the code editor under **Code source** should have updated to show the code stored in the zip file.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/source.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=b0e9c090ece970d4bd110b82a0420ca9" alt="The code of the Lambda function that was uploaded" width="1157" height="761" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/source.png" />
</Frame>

### Configure environment variables

Next, you need to set the PlanetScale `DATABASE_URL` environment variable that you copied earlier. Select the **Configuration** tab, and click **Edit**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/configuration.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=27df1ca8b7351d0dcf49f7da946f70fe" alt="The configuration tab" width="1167" height="820" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/configuration.png" />
</Frame>

You’ll be presented with a view to add or update environment variables. Click **Add environment variable** and the view will update with a row to add an environment variable. Set the **Key** field to **DATABASE\_URL** and the **Value** to the connection string taken from the previous section. Click **Save** once finished.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/environment-variables.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=faaf49570dc93d29ef979d9ee04053ab" alt="The view to manage environment variables" width="841" height="525" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/environment-variables.png" />
</Frame>

Finally, test the function by selecting the **Test** tab, and then clicking the **Test** button.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/test.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=395b8104f5c86c154d0b6ae5ef3c08f6" alt="The test tab" width="1148" height="494" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/test.png" />
</Frame>

An **Execution results** box will display above the **Test event** section. If the box is green, it likely means everything executed as expected. Click the dropdown next to **Details** to see the results of the query. Since the results of the query were logged out to the console, they will be displayed in the **Log output** section.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/aws-lambda-connection-strings/success.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=e2bc56e236240c8ec2faf45ae5db578d" alt="The execution results" width="1163" height="906" data-path="images/assets/docs/tutorials/aws-lambda-connection-strings/success.png" />
</Frame>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
