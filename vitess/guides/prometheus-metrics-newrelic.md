---
url: https://planetscale.com/docs/vitess/guides/prometheus-metrics-newrelic
title: "Prometheus Metrics Newrelic"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Sending Prometheus Metrics to New Relic

> If you're looking for your PlanetScale database metrics in your New Relic account, this tutorial will show how to configure a [Prometheus](https://prometheus.io/) instance to scrape PlanetScale's [Prometheus infrastructure](../integrations/prometheus.md) automatically, allowing you to collect detailed metrics for all of your PlanetScale branches.

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

While this tutorial is written for New Relic, using Prometheus' remote write is a common pattern for sending metrics to [AWS Managed Prometheus](https://aws.amazon.com/prometheus/), [Google Cloud Managed Service for Prometheus](https://cloud.google.com/stackdriver/docs/managed-prometheus), [Grafana hosted Prometheus](https://grafana.com/products/cloud/metrics/) and many other tools.

For more information on Prometheus Remote Write and New Relic, see the [New Relic documentation on sending Prometheus metric data](https://docs.newrelic.com/docs/infrastructure/prometheus-integrations/get-started/send-prometheus-metric-data-new-relic/).

## Overview

In this tutorial, we will be using an instance of [Prometheus](https://prometheus.io/) running on a Linux VM to scrape metrics from PlanetScale and then forward them to New Relic using [Remote Write](https://prometheus.io/docs/specs/prw/remote_write_spec/). We will make sure that Prometheus stays running by creating a [Systemd Unit File](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files).

The default configuration we will create will send all PlanetScale metrics to New Relic, and we will cover how to filter to drop certain metrics that may not be desired.

In order to proceed, you'll need:

* A [New Relic API Key](https://docs.newrelic.com/docs/apis/intro-apis/new-relic-api-keys/), make sure it is the `Ingest - License` type.
* PlanetScale [Service token](../../api/reference/service-tokens.md) with `read_metrics_endpoints` permissions.

## Prometheus Installation

First, let's download the latest release of Prometheus and create our user that is going to run it. We'll be using the latest 3.x release from the [GitHub Releases Page](https://github.com/prometheus/prometheus/releases).

Create a `prometheus` user:

```bash theme={null}
$ sudo useradd -M -U prometheus
```

```bash theme={null}
$ wget https://github.com/prometheus/prometheus/releases/download/v3.2.1/prometheus-3.2.1.linux-amd64.tar.gz
$ tar xf prometheus-3.2.1.linux-amd64.tar.gz
$ sudo mv prometheus-3.2.1.linux-amd64/ /opt/prometheus
$ sudo chown prometheus:prometheus -R /opt/prometheus
```

This has put the Prometheus binary in `/opt/prometheus` along with the example configuration file that we can use.

## Create our Systemd Unit File

Now that we have the binary in place, let's setup Systemd to run Prometheus by creating a Unit File in `/etc/systemd/system/prometheus.service` with the following contents:

```ini expandable theme={null}
[Unit]
Description=Prometheus Agent
Documentation=https://prometheus.io/
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Restart=on-failure
ExecStart=/opt/prometheus/prometheus \
  --config.file=/opt/prometheus/prometheus.yml \
  --storage.agent.path=/opt/prometheus/data \
  --web.listen-address=0.0.0.0:9091 \
  --agent

[Install]
WantedBy=multi-user.target
```

## Configure Prometheus

Now that we've got Prometheus installed and a unit file present, let's configure Prometheus. We will be borrowing some of our configuration from the [Prometheus Guide](../integrations/prometheus.md), and adding some New Relic specific configuration. Edit `/opt/prometheus/prometheus.yml` in your editor of choice so that it contains this, making sure to replace your org name, service token information, and New Relic API key:

```yaml theme={null}
global:
  scrape_interval: 1m
scrape_configs:
  - job_name: "${ORG}"
    http_sd_configs:
    - url: https://api.planetscale.com/v1/organizations/${ORG}/metrics
      authorization:
        type: "token"
        credentials: "${TOKEN_ID}:${TOKEN}"
      refresh_interval: 10m
remote_write:
  - url: https://metric-api.newrelic.com/prometheus/v1/write?prometheus_server=planetscale
    authorization:
      credentials: ${NEW_RELIC_API_TOKEN}
```

This configuration file does the following:

* Configures Prometheus to discover scraping endpoints from the PlanetScale API using a service token
* Points Prometheus to write the metrics it scrapes from PlanetScale to the New Relic API

## Starting Prometheus

Now that we have a Systemd unit file and a configured Prometheus, let's run it!

```bash theme={null}
$ sudo systemctl daemon-reload
$ sudo systemctl start prometheus.service
```

We can also tell Systemd to run Prometheus when my VM boots:

```bash theme={null}
$ sudo systemctl enable prometheus.service
```

Now, let's check to make sure everything is running properly:

```bash expandable theme={null}
$ sudo systemctl status prometheus.service
● prometheus.service - Prometheus Agent
     Loaded: loaded (/etc/systemd/system/prometheus.service; enabled; preset: enabled)
     Active: active (running) since Fri 2025-04-04 17:36:57 UTC; 38s ago
       Docs: https://prometheus.io/
   Main PID: 745542 (prometheus)
      Tasks: 9 (limit: 9486)
     Memory: 21.2M (peak: 21.9M)
        CPU: 264ms
     CGroup: /system.slice/prometheus.service
             └─745542 /opt/prometheus/prometheus --config.file=/opt/prometheus/prometheus.yml --storage.agent.path=/opt/prometheus/data --web.listen-address=0.0.0.0:9091 --agent

Apr 04 17:36:57 ubuntu prometheus[745542]: time=2025-04-04T17:36:57.804Z level=INFO source=main.go:1305 msg=EXT4_SUPER_MAGIC
Apr 04 17:36:57 ubuntu prometheus[745542]: time=2025-04-04T17:36:57.804Z level=INFO source=main.go:1308 msg="Agent WAL storage started"
Apr 04 17:36:57 ubuntu prometheus[745542]: time=2025-04-04T17:36:57.804Z level=INFO source=main.go:1437 msg="Loading configuration file" filename=/opt/prometheus/prometheus.yml
Apr 04 17:36:57 ubuntu prometheus[745542]: time=2025-04-04T17:36:57.805Z level=INFO source=watcher.go:225 msg="Starting WAL watcher" component=remote remote_name=fbe64a url="https://metric-api.newrelic.com/prometheus/v1/write?prometheus_server=planetscal>
Apr 04 17:36:57 ubuntu prometheus[745542]: time=2025-04-04T17:36:57.805Z level=INFO source=metadata_watcher.go:90 msg="Starting scraped metadata watcher" component=remote remote_name=fbe64a url="https://metric-api.newrelic.com/prometheus/v1/write?prometh>
Apr 04 17:36:57 ubuntu prometheus[745542]: time=2025-04-04T17:36:57.805Z level=INFO source=watcher.go:277 msg="Replaying WAL" component=remote remote_name=fbe64a url="https://metric-api.newrelic.com/prometheus/v1/write?prometheus_server=planetscale" queu>
Apr 04 17:36:57 ubuntu prometheus[745542]: time=2025-04-04T17:36:57.806Z level=INFO source=main.go:1476 msg="updated GOGC" old=100 new=75
Apr 04 17:36:57 ubuntu prometheus[745542]: time=2025-04-04T17:36:57.806Z level=INFO source=main.go:1486 msg="Completed loading of configuration file" db_storage=791ns remote_storage=610.171µs web_handler=897ns query_engine=301ns scrape=517.175µs scrape_s>
Apr 04 17:36:57 ubuntu prometheus[745542]: time=2025-04-04T17:36:57.806Z level=INFO source=main.go:1213 msg="Server is ready to receive web requests."
```

This reports that prometheus is `active (running)`, and I can see the logs showing that it started successfully. Great!

## Querying on New Relic

After a couple of minutes, head over to your New Relic dashboard and we can query for your database metrics. First, let's get a list of the database branches in the `nick` organization that I'm using to test:

```bash theme={null}
$ pscale branch list test --org nick
  ID             NAME         PARENT BRANCH   REGION    PRODUCTION   SAFE MIGRATIONS   READY   CREATED AT     UPDATED AT
 -------------- ------------ --------------- --------- ------------ ----------------- ------- -------------- ----------------
  7wxuxewx4l0p   main         n/a             us-east   Yes          No                Yes     2 years ago    50 minutes ago
  6o0rr27785fl   partitions   main            us-east   No           No                Yes     2 months ago   7 minutes ago
```

For this, we'll use the `7wxuxewx4l0p` branch.

Using New Relic's NRQL, we can visualize the memory usage of my VTTablet instances with the following query:

```sql theme={null}
FROM Metric SELECT average(planetscale_pods_cpu_util_percentages) WHERE planetscale_database_branch_id = '7wxuxewx4l0p' AND planetscale_component='vttablet' SINCE 30 minutes AGO TIMESERIES FACET planetscale_pod
```

Because my `main` branch is production, we will see the memory usage for my primary and both my replicas over the last 30 minutes:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/xsX1e-5IXCYXbX59/vitess/tutorials/metrics-new-relic-dashboard.png?fit=max&auto=format&n=xsX1e-5IXCYXbX59&q=85&s=5ae095318cdab9644ca82f12f11ad58f" alt="New Relic Memory Query" width="2758" height="1450" data-path="vitess/tutorials/metrics-new-relic-dashboard.png" />
</Frame>

## Filtering Metrics

If you don't want to ingest every metric into New Relic, you can tell Prometheus to drop certain metrics. For more information, see the [New Relic Documentation](https://docs.newrelic.com/docs/infrastructure/prometheus-integrations/install-configure-remote-write/set-your-prometheus-remote-write-integration/#allow-deny).

If we adjust our Prometheus configuration that we have in `/opt/prometheus/prometheus.yml` we can instruct Prometheus to drop all metrics unless they match a certain naming convention:

```yaml theme={null}
remote_write:
  - url: https://metric-api.newrelic.com/prometheus/v1/write?prometheus_server=planetscale
    authorization:
      credentials: ${NEW_RELIC_API_TOKEN}
  - source_labels: [__name__]
    regex: "planetscale_pods_(.*)"
    action: keep
```

If you replace your `remote_write` block with what's above, Prometheus will only forward the timeseries that match the `planetscale_pods_*` name. For a full list of metrics, see our [Metric List](../integrations/prometheus-metrics.md).

## What's Next?

Now that you have your branch metrics in New Relic, you can create dashboards and alerts for conditions such as high CPU or replication delay.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
