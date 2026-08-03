---
url: https://planetscale.com/docs/vitess/guides/prometheus-metrics-grafana
title: "Prometheus Metrics Grafana"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

## Introduction

This guide requires that you’ve set up a Prometheus instance from our documentation.

If you’re already running Grafana in production and you’re just looking for our standard dashboard template, you can find it [on GitHub](https://github.com/planetscale/grafana-dashboard).

## Install Grafana

Grafana’s [installation documentation](https://grafana.com/docs/grafana/latest/setup-grafana/installation/) contains information for their supported platforms. For this guide, we’ll be setting this up locally on a macOS machine.

If you’re using a hosted Grafana option such as [Grafana Cloud](https://grafana.com/products/cloud/) or [AWS Managed Grafana](https://aws.amazon.com/grafana/) you can skip this step.

On macOS, Grafana is availabile via [homebrew](https://brew.sh/), and I can install it with:

```shellscript
$ brew install grafana
```

This will download and install Grafana, and I can start it with:

```shellscript
$ brew services start grafana
```

When that succeeds, I can go to `http://localhost:3000/` and I should see the Grafana welcome page:

![Grafana Welcome Page](https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/tutorials/metrics-grafana-welcome.png?w=2500&fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=bfd15c6dd885790f23bb2958b5caa17a)

Grafana Welcome Page

The default username and password for a new install is `admin` and `admin`. Grafana will ask you to change the password the first time you log in, please pick something more secure than `admin`.

### Adding a Prometheus Endpoint

You can skip this step as well if you’re already running a managed Prometheus or have added your datasource to Grafana already.

If you’re running Prometheus locally, you’ll need to add that as a datasource. To do this:

Now, you should look see a page that looks like this:

![Grafana Add Datasource](https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/tutorials/metrics-add-prometheus-connection.png?w=2500&fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=d8ba77f8190a6b387f976398b9ec0ce3)

Grafana Add Datasource

You can call this whatever you want, we’ll use the following:

- Name: “PlanetScale”
- Prometheus server URL: `http://localhost:9090/`

![Grafana Prometheus Configuration](https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/tutorials/metrics-prometheus-configuration.png?w=2500&fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=330e9dce03b1afc6d9587df3486400b8)

Grafana Prometheus Configuration

Because this is running on your local machine, we do not need to use any Authentication or TLS

Scroll down to the “Interval behaviour” section and set the “Scrape interval” to `1m`.

Finally, scroll to the bottom and click “Save & test”.

## Import the PlanetScale Dashboard

Now that we have our datasource added, let’s import the PlanetScale Dashboard. This is a starter dashboard that PlanetScale has produced which shows an overview of your branch with the metrics that we expose.

From the Grafana homepage, go to the top left menu and pick “Dashboards”.

In the top right, click “New” and then Import”:

PlanetScale maintains the latest version of the dashboard located here:

[https://github.com/planetscale/grafana-dashboard/blob/main/overview.json](https://github.com/planetscale/grafana-dashboard/blob/main/overview.json)

Download this file to your computer, and then click “Upload dashboard JSON file”.

Find the JSON file you downloaded in the previous step, and configure it with the Prometheus datasource that we added in an earlier:

![Grafana Prometheus Configuration](https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/tutorials/metrics-prometheus-configuration.png?w=2500&fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=330e9dce03b1afc6d9587df3486400b8)

Grafana Prometheus Configuration

Click ‘Import’ and you should be directed to the dashboard, configured to query your local Prometheus with the data it’s been scraping!

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
