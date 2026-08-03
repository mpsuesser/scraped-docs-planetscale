---
url: https://planetscale.com/docs/vitess/integrations/vantage
title: "Vantage"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

After integrating, you can create [cost reports](https://docs.vantage.sh/cost_reports) to break down costs per database and branch.

Beyond reporting, set up budget alerts, forecast usage, and view active database costs in Vantage. Vantage connects to your PlanetScale organizations using an OAuth flow.

## Prerequisites

- The [Organization Admin role](../../security/access-control.md) in PlanetScale
- A [Vantage account](https://console.vantage.sh/signup)

Database cost reporting in Vantage is not available for [PlanetScale Managed](../../plans/managed.md) customers via the integration.

## Configure the Vantage integration

Costs will be ingested and processed in Vantage once you add the integration. It typically takes less than 15 minutes to ingest PlanetScale costs. The costs will be available on your **All Resources** Cost Report in Vantage as soon as they are processed.

PlanetScale data refreshes daily in Vantage.

## View PlanetScale costs in Vantage

In Vantage, you can create cost reports to drill down into your costs. Vantage displays PlanetScale costs by Organization, Service, Category, and Resource.

![Image of a PlanetScale Cost Report in Vantage showing costs per database](https://mintcdn.com/planetscale-2/UzFO5Pe10M0-W-uW/images/assets/docs/integrations/vantage/vantage-console.png?w=2500&fit=max&auto=format&n=UzFO5Pe10M0-W-uW&q=85&s=986bf10d22180bc5c9047600caf39db3)

Image of a PlanetScale Cost Report in Vantage showing costs per database

In the graphic above, PlanetScale costs are grouped by database for the month. For complete cost reporting dimensions and more information, see the [PlanetScale documentation](https://docs.vantage.sh/connecting_planetscale) for Vantage.

## Billing

The Vantage integration is available on all our [plans](../../planetscale-plans.md).

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
