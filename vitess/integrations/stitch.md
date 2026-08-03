---
url: https://planetscale.com/docs/vitess/integrations/stitch
title: "Stitch"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

We implemented an [Stitch Singer Tap](https://stitchdata.com/) as the pipeline between your PlanetScale source and selected destination. This document will walk you through how to connect your PlanetScale database to Stitch.

## How to connect

### Step 1: Setup an Import API integration in Stitch.

PlanetScale’s Stitch tap outputs records and metadata to stdout so that the `http tap` can import them into Stitch via [Stitch Import API](https://www.stitchdata.com/docs/developers/import-api/).

### Step 2: Configure the PlanetScale Stitch Tap

In this step, we will connect your PlanetScale database to the PlanetScale Singer Tap.

### Step 3: Run the PlanetScale Stitch Tap

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
