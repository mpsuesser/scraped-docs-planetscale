---
url: https://planetscale.com/docs/vitess/tutorials/planetscale-serverless-driver-node-example
title: "Planetscale Serverless Driver Node Example"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

This guide will be using VS Code as the IDE, but you may use your preferred IDE.

## Use the sample repository

We offer a sample repository that can be used as an educational resource. It is an Express API that can be run locally with sample `SELECT`, `INSERT`, `UPDATE`, and `DELETE` statements mapped to the proper API endpoints.

To follow along, you’ll need the following:

- A PlanetScale account, as well as knowing how to create a database.
- The PlanetScale CLI is installed on your computer, which will be used to seed data.

Start by creating a database in PlanetScale by clicking **“New database”** > **“Create new database”**.

![How to create a new database. priority](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/how-to-create-a-new-database-2.png?w=2500&fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=9bce4125202d23e2966f4237b2a35006)

How to create a new database. priority

Name the database `travel_db`. Click **“Create database”**. Wait for the database to finish initializing before moving on.

![The travel_db initializing.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/initializing.png?w=2500&fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=8f6e9f45a92206e3d1ed1ff50173e578)

The travel\_db initializing.

Generate a set of credentials by clicking the **“Connect”** button.

![The Connect button in the PlanetScale dashboard.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/the-connect-button-in-the-planetscale-dashboard.png?w=2500&fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=6ec03a0ef53af9c64ad5ec8c34bf47e4)

The Connect button in the PlanetScale dashboard.

Copy your password credentials first:

![The password details.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/the-serverlessjs-connect-modal.png?w=2500&fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=e2234b65ee8937cd81fa5cde6c68b96a)

The password details.

Scroll down and select **“database-js”** from the “Select your language or framework” options. Copy the text from the **“.env”** section, as we’ll be putting this in the project after it’s pulled down from GitHub.

![The password env details.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/the-serverlessjs-connect-modal-env.png?w=2500&fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=6f94949610872efafea96789ddd3615a)

The password env details.

On your workstation, open a terminal and clone the repository to your computer by running the following command:

```shellscript
git clone https://github.com/planetscale/database-js-starter
```

Navigate to the `scripts` folder and run the `seed_database.sh` script to populate a small database simulating a travel agency.

```shellscript
cd database-js-starter/scripts
./seed-database.sh
```

If you are using Windows, run this command through the [Windows Subsystem for Linux (WSL)](https://docs.microsoft.com/en-us/windows/wsl/)

Create a new file named `.env` in the root of the project and paste in the sample provided from PlanetScale that you copied earlier.

To run the project, run the following commands from the root of the project.

```shellscript
npm install
npm start
```

If the project is running properly, you should receive a message stating that the API is running.

The `tests.http` file is designed to work with the [VS Code Rest Client plugin](https://marketplace.visualstudio.com/items?itemName=humao.rest-client), but can be used as a reference when testing with the tool of your choosing. If you are using the plugin, you may click the **“Send request”** button that appears above each request to see the API in action.

![An example of a POST request to the sample project.](https://mintcdn.com/planetscale-2/AJPY38bILe2zenXX/images/assets/docs/tutorials/planetscale-serverless-driver-node-example/an-example-of-a-post-request-to-the-sample-project.png?w=2500&fit=max&auto=format&n=AJPY38bILe2zenXX&q=85&s=70c6a61629fbacbcc4c69d9509ae8f02)

An example of a POST request to the sample project.

If you check the terminal where the API was started, the response from the `execute` function is logged out for review.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
