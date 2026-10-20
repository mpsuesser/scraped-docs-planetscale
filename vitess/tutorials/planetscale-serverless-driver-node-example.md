---
url: https://planetscale.com/docs/vitess/tutorials/planetscale-serverless-driver-node-example
title: "Planetscale Serverless Driver Node Example"
description: ""
access_date: 2026-10-20T18:01:41.586Z
current_date: 2026-10-20T18:01:41.586Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Node.js example using the PlanetScale serverless driver

> This guide will cover how to use the provided Node.js sample application using the PlanetScale serverless driver for JavaScript.

## Overview

> [!NOTE]
>
> This guide will be using VS Code as the IDE, but you may use your preferred IDE.

## Use the sample repository

We offer a sample repository that can be used as an educational resource. It is an Express API that can be run locally with sample `SELECT`, `INSERT`, `UPDATE`, and `DELETE` statements mapped to the proper API endpoints.

To follow along, you’ll need the following:

* A PlanetScale account, as well as knowing how to create a database.
* The PlanetScale CLI is installed on your computer, which will be used to seed data.

Start by creating a database in PlanetScale by clicking **"New database"** > **"Create new database"**.

![How to create a new database. priority](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/how-to-create-a-new-database-2.png?fit=max\&auto=format\&n=AJPY38bILe2zenXX\&q=85\&s=beb148475e5dfe5db5f24934b11ad022)

Name the database `travel_db`. Click **"Create database"**. Wait for the database to finish initializing before moving on.

![The travel\_db initializing.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/initializing.png?fit=max\&auto=format\&n=AJPY38bILe2zenXX\&q=85\&s=5fc78b39b71f92b2fe184b2478fb5a84)

Generate a set of credentials by clicking the **"Connect"** button.

![The Connect button in the PlanetScale dashboard.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/the-connect-button-in-the-planetscale-dashboard.png?fit=max\&auto=format\&n=AJPY38bILe2zenXX\&q=85\&s=2fd16015214563dbbc2b6b06a1c2c402)

Copy your password credentials first:

![The password details.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/the-serverlessjs-connect-modal.png?fit=max\&auto=format\&n=AJPY38bILe2zenXX\&q=85\&s=f73a60ddc00f0cb91e3cae3555e92e05)

Scroll down and select **"database-js"** from the "Select your language or framework" options. Copy the text from the **".env"** section, as we'll be putting this in the project after it's pulled down from GitHub.

![The password env details.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/the-serverlessjs-connect-modal-env.png?fit=max\&auto=format\&n=AJPY38bILe2zenXX\&q=85\&s=a0eedb7b2b936dbf75a9d8bca97148b1)

On your workstation, open a terminal and clone the repository to your computer by running the following command:

```bash theme={null}
git clone https://github.com/planetscale/database-js-starter
```

Navigate to the `scripts` folder and run the `seed_database.sh` script to populate a small database simulating a travel agency.

```bash theme={null}
cd database-js-starter/scripts
./seed-database.sh
```

> [!NOTE]
>
> If you are using Windows, run this command through the [Windows Subsystem for Linux (WSL)](https://docs.microsoft.com/en-us/windows/wsl/)

Create a new file named `.env` in the root of the project and paste in the sample provided from PlanetScale that you copied earlier.

To run the project, run the following commands from the root of the project.

```bash theme={null}
npm install
npm start
```

If the project is running properly, you should receive a message stating that the API is running.

The `tests.http` file is designed to work with the [VS Code Rest Client plugin](https://marketplace.visualstudio.com/items?itemName=humao.rest-client), but can be used as a reference when testing with the tool of your choosing. If you are using the plugin, you may click the **"Send request"** button that appears above each request to see the API in action.

![An example of a POST request to the sample project.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/an-example-of-a-post-request-to-the-sample-project.png?fit=max\&auto=format\&n=AJPY38bILe2zenXX\&q=85\&s=d1eeca2575f13571f643ef7af27003e8)

If you check the terminal where the API was started, the response from the `execute` function is logged out for review.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
