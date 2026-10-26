---
url: https://planetscale.com/docs/vitess/integrations/vantage
title: "Vantage"
description: ""
access_date: 2026-10-26T19:24:46.643Z
current_date: 2026-10-26T19:24:46.643Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Vantage integration

> With [Vantage](https://www.vantage.sh/), you can set up [PlanetScale cost management](https://vantage.sh/integrations/planetscale) to report on PlanetScale costs alongside other infrastructure providers, such as AWS or GCP.

This page covers Vitess.

After integrating, you can create [cost reports](https://docs.vantage.sh/cost_reports) to break down costs per database and branch.

Beyond reporting, set up budget alerts, forecast usage, and view active database costs in Vantage. Vantage connects to your PlanetScale organizations using an OAuth flow.

## Prerequisites

* The [Organization Admin role](https://planetscale.com/docs/security/access-control.md) in PlanetScale
* A [Vantage account](https://console.vantage.sh/signup)

> [!NOTE]
>
> Database cost reporting in Vantage is not available for [PlanetScale Managed](https://planetscale.com/docs/plans/managed.md) customers via the integration.

## Configure the Vantage integration

**Step 1**

From the Vantage console, navigate to the [Integrations page](https://console.vantage.sh/settings/integrations).

**Step 2**

Select **PlanetScale**, then click **Connect PlanetScale Account**.

**Step 3**

The PlanetScale login screen is displayed. Log in to your PlanetScale account and select the organizations you want to connect with.

**Step 4**

Click **Authorize access**.

**Step 5**

On the [PlanetScale Settings](https://console.vantage.sh/settings/planetscale/) page in Vantage, you should see the status of your connection change to **Importing**.

Costs will be ingested and processed in Vantage once you add the integration. It typically takes less than 15 minutes to ingest PlanetScale costs. The costs will be available on your **All Resources** Cost Report in Vantage as soon as they are processed.

> [!NOTE]
>
> PlanetScale data refreshes daily in Vantage.

## View PlanetScale costs in Vantage

In Vantage, you can create cost reports to drill down into your costs. Vantage displays PlanetScale costs by Organization, Service, Category, and Resource.

![Image of a PlanetScale Cost Report in Vantage showing costs per database](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/vantage/vantage-console.png?fit=max\&auto=format\&n=UzFO5Pe10M0-W-uW\&q=85\&s=6018bb6488c79a20268f102876b221a8)

In the graphic above, PlanetScale costs are grouped by database for the month. For complete cost reporting dimensions and more information, see the [PlanetScale documentation](https://docs.vantage.sh/connecting_planetscale) for Vantage.

## Billing

The Vantage integration is available on all our [plans](https://planetscale.com/docs/planetscale-plans.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
