---
url: https://planetscale.com/docs/vitess/imports/postgres-mysql-planetscale-migration-guide
title: "Postgres Mysql Planetscale Migration Guide"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Postgres to MySQL to PlanetScale migration guide

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

<Warning>
  **PlanetScale now supports Postgres**. This guide is for migrating from Postgres to PlanetScale's Vitess product. You can still use these scripts if you would like to utilize [Vitess](../../vitess.md). If you prefer to stay on Postgres, refer to the [Postgres import guides](../../postgres/imports/postgres-imports.md).
</Warning>

This guide covers how to import a Postgres database to PlanetScale for Vitess using an intermediate Aurora MySQL database.
For this, we will be using the [postgres-mysql-planetscale scripts](https://github.com/planetscale/migration-scripts/tree/main/postgres-mysql-planetscale).
This technique is good for larger databases that need a fast import, but requires an intermediate MySQL instance.
If you have a smaller database or don't mind a slower import, consider [an alternate approach](postgres-planetscale-migration-guide.md).
You are encouraged to modify these scripts to suit your needs if need be.

## Methodology

The scripts in this guide leverage [AWS Database Migration Service](https://aws.amazon.com/dms/) to handle conversions between Postgres and MySQL types.
It also creates a new Aurora MySQL database to use as a go-between for Postgres and PlanetScale.
Even if you are migrating from a non-AWS Postgres provider, such as Neon or Supabase, you will still need an AWS account to perform the migration.

When using this script, your data will take the following path:

* Data flows from your Postgres source into DMS
* DMS does necessary type conversions and copies the data into the Aurora MySQL database
* Using the PlanetScale import tool, your data will flow from Aurora MySQL into your destination PlanetScale database
* After the initial copy, changes will continue to flow form Postgres, to Aurora MySQL, to PlanetScale so that your data stays in sync, even if the migration takes several hours or days.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/g0AZZQkXmTSBYuKj/images/postgres-mysql-planetscale-darkmode.png?fit=max&auto=format&n=g0AZZQkXmTSBYuKj&q=85&s=165c4b036dfb0c8b39ac411a34ba36cd" alt="Import data flow" width="3202" height="812" data-path="images/postgres-mysql-planetscale-darkmode.png" />
</Frame>

It is up to you to determine how to handle the cut over between the two in your application.

### Prerequisites

* An AWS account
* An empty PlanetScale database as the target
* The [AWS CLI](https://aws.amazon.com/cli/)

<Warning>
  These import scripts create and modify resources in your AWS account.
  Before executing, you should read through the scripts to ensure you are comfortable with the actions they will take.
  You will also be billed in AWS for the resources it creates, which include:

  * 1 DMS replication task
  * 1 DMS replication instance
  * 1 DMS replication subnet group
  * 2 DMS endpoints
  * 1 Aurora MySQL database
</Warning>

## Importing a database

### 1. Prepare Postgres for migration

Before beginning a migration to PlanetScale, you should ensure the following flags are set on your Postgres database.

| flag name                  | flag value |
| -------------------------- | ---------- |
| logical\_replication       | 1          |
| shared\_preload\_libraries | pglogical  |

Given the variety of Postgres options and versions, there may be additional flags that you will need to adjust to make the import work.
If you encounter errors when using the script below, it may be caused by other Postgres options not shown here.
You can either update your Postgres configuration based on the error you see, or [contact support](https://planetscale.com/contact?initial=support) for additional assistance.

<Warning>
  You should not make any schema changes to the source database during an import.
</Warning>

### 2. Create an EC2 instance

These scripts are designed to be run from an EC2 instance on the same account that you will authenticate into.
Create one, and then log in to the instance.
Ensure that both Postgres and MySQL are installed:

```
sudo apt update
sudo apt install postgresql
sudo apt install mysql-server
```

### 3. Install the AWS CLI

The migration scripts we provide rely on [AWS Database Migration Service](https://aws.amazon.com/dms/).
To use the scripts, you will need to download and install the [AWS CLI](https://aws.amazon.com/cli/).
You will also need to authenticate into the AWS account that you would like to run the migration from.
This step is necessary, even if you are importing from a non-AWS Postgres provider.

Go ahead and download and install the AWS CLI on the EC2 instance you created in the last step.

For example, on **Ubuntu**, you would run:

```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Full instructions can be found on the [AWS documentation](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).

### 4. Authenticate into AWS

After installing the CLI, you must authenticate into the AWS account that you intend to run the import scripts in.
There are several ways to authenticate.
You can find instructions in the [AWS documentation](https://docs.aws.amazon.com/cli/v1/userguide/cli-chap-authentication.html).
We recommend you authenticate with [short-term credentials](https://docs.aws.amazon.com/cli/v1/userguide/cli-authentication-short-term.html).

The authenticated account will need permissions to both create and modify DMS resources, RDS / Aurora databases, security groups, and parameter groups

### 5. Prepare the import

Check out the [migration-scripts](https://github.com/planetscale/migration-scripts) repository, and navigate to the `postgres-mysql-planetscale` directory.

```
git clone https://github.com/planetscale/migration-scripts
cd migration-scripts/postgres-mysql-planetscale
```

Since these scripts will be running in your AWS account, it is important that you understand what the scripts are doing before executing them.
Before running, look through the scripts and confirm what they will be doing in your AWS account is acceptable to you and your organization.
At a high level, this script does the following:

1. Creates a DMS source using the Postgres credentials you provide
2. Creates a new Aurora MySQL database and sets it as the target for the DMS import
3. Creates a DMS import instance (a server to handle the migration)
4. Sets up rules for how to handle the migration

If there are any concerns, you should modify the scripts to suit your needs.

<Warning>
  This script prepares for a migration between your postgres source and a new MySQL database. The MySQL database will be accessible from all IPs.

  If you want a tighter security configuration, modify the script to make the database only accessible from the [required PlanetScale IPs](import-tool-migration-addresses.md).
</Warning>

If you are comfortable proceeding, the next step is to execute the `prepare.sh` script.
You will need to provide this with a unique identifier for this import, as well as the connection credentials for the source Postgres database.

<Note>
  If you are importing from **Supabase**, the scripts will not work with a transaction pooler or session pooler connection.
  You must use a direct connection over ipv4.
  In order to use this, you must be on the pro plan or greater, and pay for the ipv4 connection upgrade.
  After doing so, use the direct connection credentials and host when using `import.sh`.

  If you are importing from **Neon**, You must use `--tls` mode when importing from Neon.
  This sets `SSL_MODE="require"` on the connection, a necessity for Neon.
</Note>

Here's an example of executing this:

```
 sh prepare.sh --identifier "PGtoPSImport01" \
   --source "${PG_USER}:${PG_PASSWORD}@${PG_HOST}/${PG_DB}/${PG_SCHEMA}" \
   --ips "us-east-1"
```

You can choose whatever identifier you want in place of `PGtoPSImport01`.
The variables prefixed with `PG_` are for the Postgres source.

Running the script like this will give you occasional log messages indicating which phase of the import process it is at.
If you want full debug mode, including each command the script executes, add the `--debug` flag:

```
 sh prepare.sh --identifier "PGtoPSImport01" \
   --source "${PG_USER}:${PG_PASSWORD}@${PG_HOST}/${PG_DB}/${PG_SCHEMA}" \
   --ips "us-east-1" \
   --debug
```

This script can take upwards of 15 minutes, particularly because of the step to set up the DMS import server.

When this script completes, it will provide the connection information for the MySQL instance and instructions for next steps.
For example:

```
======================================================================================
SETUP COMPLETED SUCCESSFULLY
======================================================================================

MySQL RDS instance information:
Hostname: TARGET_HOSTNAME
Database: TARGET_DATABASE

Admin user:
Username: TARGET_USERNAME
Password: TARGET_PASSWORD

Migration user (for PlanetScale):
Username: migration_user
Password: MIGRATION_PASSWORD

======================================================================================
NEXT STEPS:
======================================================================================
1. Log into your new MySQL instance using the credentials above
2. Set up your schema manually
3. Once your schema is ready, run the start.sh script with the same identifier:
   sh start.sh --identifier \"$IDENTIFIER\"
```

Notably, this step does not actually begin the migration, but sets up the necessary AWS and DMS resources.

<Note>
  If your database is on a PlanetScale cloud or managed plan, you will need to manually provide your IP addresses.
  For this, use `--ips "manual"` and then give the script a comma-separated list of IPs as instructed by the script.
</Note>

### 6. Copy your schema

We have configured these scripts so that they do not automatically copy the schema from Postgres to MySQL.
This is intentional, as DMS sometimes does not make good choices for how to convert Postgres types to MySQL.
Therefore, we leave it up to you to copy the schema before we begin the migration via `import.sh`.

There are several ways you can do this, but one option is to use `pg_dump` to get your schema, convert to MySQL types / syntax, the apply it to the MySQL target.
First, you'd run a `pg_dump` command:

```
PGPASSWORD=${PG_PASSWORD} pg_dump -h ${PG_HOST} -U ${PG_USERNAME} ${PG_DATABASE} --schema-only --format=p > schema.sql
```

Next, modify `schema.sql`.
Remove all excessive lines from the dump, and update all column types to use ones supported by MySQL.

Finally, apply this schema to a new database in the MySQL target created by the `prepare.sh` script:

```
echo "CREATE DATABASE ${MYSQL_DATABASE}" | mysql -u ${MYSQL_USER} -p${MYSQL_PASSWORD} -h${MYSQL_HOST}
cat schema.sql | mysql -u ${MYSQL_USER} -p${MYSQL_PASSWORD} -h${MYSQL_HOST} ${MYSQL_DATABASE}
```

Then, log in to the MySQL instance to confirm the schema was applied correctly.

### 7. Migration from Postgres to Aurora MySQL

Next, we need to run `start.sh`.
This initiates the migration between the Postgres source and the Aurora MySQL target.
To start, just run:

```
start.sh --identifier "PGtoPSImport01"
```

You can monitor the progress of the migration by comparing row counts between the source and target database.
The initial data copy may range from several minutes to several hours depending on the size of your database.
After the initial copy, data changes to your Postgres database will replicate to Aurora MySQL.
You do not need to connect to the Aurora MySQL database form your application.
We are only using it as a temporary holding-place while moving into PlanetScale.

### 8. The PlanetScale import tool

To get your data into PlanetScale, we will use the import tool to migrate the data from the Aurora MySQL instance created in the previous steps into PlanetScale.
Log into PlanetScale, select your organization, click "New database", and then "Import database."

<Frame>
  <img src="https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/imports/planetscale-new-import.png?fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=0bd12e35cd5dc962daacc98b2d47f8b9" alt="PlanetScale new database via import" width="3024" height="1122" data-path="vitess/imports/planetscale-new-import.png" />
</Frame>

Enter the name of your database and choose the type and size of database you want to import to.
For large imports, we recommend using Metal for improved import speed.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/imports/planetscale-database-name-type.png?fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=82022806c4f232a69662037e5a289b5a" alt="PlanetScale set database name and type for import" width="3024" height="1420" data-path="vitess/imports/planetscale-database-name-type.png" />
</Frame>

Scroll down and you will see a section to add the connection information for the database to import.
Enter all of the credentials that the script printed out, and use the username and password for the `migration_user`.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/imports/planetscale-import-connection-info.png?fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=954d8ecdcb4fd83067404a8d18a22f5e" alt="PlanetScale import connection info" width="2060" height="1774" data-path="vitess/imports/planetscale-import-connection-info.png" />
</Frame>

Click "Connect to Database."
If you encounter any errors at this step, look carefully at the error message and address any connection or schema issues as needed.
When the import is ready, click "Begin import."

### 9. Complete the import

This full import flow not only copies data, but does continuous replication of traffic from Postgres, to DMS, to Aurora MySQL, and finally to PlanetScale.
Replication between these continues until you stop the DMS task using `cleanup.sh` and complete the PlanetScale import flow.

It is up to you to determine how you want to cut over your application to use PlanetScale as your primary database instead of the old Postgres source.
Before doing this, you should ensure all of your queries and/or your ORM are updated to work properly with PlanetScale.
We also recommend doing some performance testing, and adding indexes if you encounter slow queries.

### 10. Clean up import

After you have switched all of your traffic over to PlanetScale and are comfortable wrapping up the import, you can clean up the resources that the script created.
This includes the DMS migration instance, the Aurora database, and the DMS source / targets.

<Warning>
  Do not run this until you are absolutely sure you no longer need the migration set up.
</Warning>

To clean up the resources, you can run:

```
sh cleanup.sh --identifier "PGtoPSImport01"
```

We recommend double checking that all of the resources were properly cleaned up in your AWS console after running this.

## Resolving errors

We have designed these scripts to run as generally as possible and have tested them on a variety of platforms.
Even so, you may encounter errors for a variety of reasons.

In some cases, the `aws` command that fails will produce a useful error message in the script output, which you can take action on as needed.

If the script is able to set up all of the resources without error but the import is not working, we recommend you take a look at the migration task logs.
You can view and search through these in the AWS web console.

Log in to the web console, and go to the AWS DMS service page.
In the sidebar, click on "Database migration tasks."

<Frame>
  <img src="https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/imports/aws-dms.png?fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=4fd7e3116d6ff25ce77b25e868e88dd9" alt="AWS DMS landing page" width="3010" height="1720" data-path="vitess/imports/aws-dms.png" />
</Frame>

You should see a migration task in the list with a name that corresponds to the identifier you chose.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/imports/aws-dms-migration-tasks.png?fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=5a83fc179853103d6407e66e3b47fef0" alt="AWS DMS migration tasks" width="3012" height="1722" data-path="vitess/imports/aws-dms-migration-tasks.png" />
</Frame>

Click on the migration task, and then click "View logs" in the top right corner.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/imports/aws-dms-migration-task-choice.png?fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=1fa56d1a8c2d4edd7e0867768945c3b9" alt="AWS DMS migration task choice" width="3012" height="1722" data-path="vitess/imports/aws-dms-migration-task-choice.png" />
</Frame>

This will bring you to your CloudWatch logs for this replication task, and you can search through it for error and warning messages to help you pinpoint the issue.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/imports/aws-dms-migration-task-logs.png?fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=e6718fc701f7354ed1c5bdfa0b7be24f" alt="AWS DMS migration task logs" width="2334" height="1722" data-path="vitess/imports/aws-dms-migration-task-logs.png" />
</Frame>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
