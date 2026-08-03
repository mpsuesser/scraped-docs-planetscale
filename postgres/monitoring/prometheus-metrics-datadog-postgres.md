---
url: https://planetscale.com/docs/postgres/monitoring/prometheus-metrics-datadog-postgres
title: "Prometheus Metrics Datadog Postgres"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Sending Prometheus Metrics to Datadog for PlanetScale Postgres

> If you're looking for more metrics than PlanetScale's native Datadog integration provides, this tutorial will show how to configure your [Datadog agent](https://docs.datadoghq.com/agent/) to scrape PlanetScale's [Prometheus infrastructure](../../vitess/integrations/prometheus-metrics.md) automatically, allowing you to collect detailed metrics for all of your PlanetScale Postgres branches.

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

<PlatformAvailability current="postgres" vitess="/vitess/monitoring/prometheus-metrics-datadog" />

## Overview

In this tutorial, this guide assumes that you have a Datadog Agent Version 7 running. For more information on what the Datadog Agent is and how to install it, start with the [Datadog Agent documentation](https://docs.datadoghq.com/getting_started/agent/).

For the purposes of this guide, the examples use a Datadog agent running with the recommended installation steps on a Linux system.

## Prerequisites

You'll need a working Datadog agent and access to add a [Custom Agent Check](https://docs.datadoghq.com/developers/custom_checks/) to that instance. This may require `root` or `sudo` access on the machine running the Datadog agent.

You'll also need a [Service token](../../api/reference/service-tokens.md) in your Organization, with the `read_metrics_endpoints` permission granted.

## Adding the Plugin to the Datadog Agent

Go to [https://github.com/planetscale/planetscale-datadog](https://github.com/planetscale/planetscale-datadog), which is the repository that has our custom OpenMetrics Check.

Place the unedited `planetscale.py` in the `checks.d` directory of your Datadog Agent.

* On Linux, that is `/etc/datadog-agent/checks.d/`
* On macOS, that is `/opt/datadog-agent/etc/checks.d/`

Make sure that it belongs to the appropriate user. If you're using the recommended Linux installation steps, it will have created a `dd-agent` user:

```bash theme={null}
$ pwd
/etc/datadog-agent/checks.d
$ ls -al planetscale.py
-rw-r--r-- 1 dd-agent dd-agent 9261 Apr  2 22:54 planetscale.py
```

This file is owned by the `dd-agent` user and group in the `/etc/datadog-agent/checks.d` directory.

If you're on macOS, it will depend on whether you installed the agent as a 'Single User Agent' or a 'Systemwide Agent'. If you picked Single User, there should be no additional permission changes needed. If you installed it as a Systemwide agent, make sure the user and group you installed the agent with have ownership of the file.

## Configuring the Datadog Agent

Now that you have the plugin installed, you need to configure it. In the `conf.d` directory of the Datadog agent take the `conf.d/planetscale.yaml.example` file and edit it with your organization name and Service Token information. It should look like this:

```yaml expandable theme={null}
instances:
  - planetscale_organization: 'your-org' # Required: Your PlanetScale organization ID
    ps_service_token_id: '${TOKEN_ID}' # Required: Your PlanetScale Service Token ID
    ps_service_token_secret: '${TOKEN}' # Required: Your PlanetScale Service Token Secret. Consider using Datadog secrets management: https://docs.datadoghq.com/agent/guide/secrets-management/

    namespace: 'planetscale_postgres' # Required: Namespace for the metrics
    metrics: # Required: List of metrics to collect. Use mapping for renaming/type overrides.
      - planetscale_postgres_connection_state: postgres_connection_state
      - planetscale_edge_postgres_active_connections: edge_active\_connections

    min_collection_interval: 60
    send_distribution_buckets: true
    collect_counters_with_distributions: true
```

This configures the integration to look for all of the branches in your PlanetScale organization, collect specific Postgres metrics, and put them inside the `planetscale_postgres` namespace.

### Collecting all metrics

If you want to collect all available PlanetScale Postgres metrics instead of specifying individual metrics, you can use a wildcard pattern:

```yaml theme={null}
instances:
  - planetscale_organization: 'your-org'
    ps_service_token_id: '${TOKEN_ID}'
    ps_service_token_secret: '${TOKEN}'

    namespace: 'planetscale_postgres'
    metrics: [".*"]

    min_collection_interval: 60
    send_distribution_buckets: true
    collect_counters_with_distributions: true
```

Using `'.*'` in the metrics list tells the Datadog agent to scrape all metrics exposed by PlanetScale. This is useful when you want comprehensive monitoring without manually maintaining a list of specific metrics.

Save the file as `planetscale.yaml`, making sure to double check permissions:

```bash theme={null}
$ pwd
/etc/datadog-agent/conf.d
$ ls -al planetscale.yaml
-rw-r--r-- 1 root root 1518 Apr  2 22:57 planetscale.yaml
```

## Restart the Datadog Agent

Now that this is configured and installed, restart the Agent:

```bash theme={null}
$ sudo systemctl restart datadog-agent
```

## Validating the PlanetScale Plugin

Now that the Datadog Agent is running the PlanetScale plugin, metrics should start flowing into Datadog within a couple of minutes. To validate, you can check the Datadog Agent:

```bash theme={null}
sudo -u dd-agent -- datadog-agent check planetscale
```

If the plugin is installed successfully, this should output the scrape targets for your branches, as well as metadata about when it was last run and how many metrics were emitted.

## Adding Metrics

In the earlier configuration, only two metrics were added. For a complete list of what PlanetScale Postgres exposes, please take a look at the [Postgres Metrics Reference Documentation](prometheus-metrics-postgres.md).

Note that the Datadog agent [normalizes metrics with certain suffixes starting in v7.32.0](https://github.com/DataDog/integrations-core/blob/master/openmetrics/README.md):

> Starting in Datadog Agent v7.32.0, in adherence to the OpenMetrics specification standard, counter names ending in \_total must be specified without the \_total suffix. For example, to collect promhttp\_metric\_handler\_requests\_total, specify the metric name promhttp\_metric\_handler\_requests. This submits to Datadog the metric name appended with .count, promhttp\_metric\_handler\_requests.count.

This means that to scrape a metric such as `planetscale_postgres_database_xact_commit_total`, you would configure the Datadog agent for `planetscale_postgres_database_xact_commit`.

If you want to collect additional metrics, you can add them to the list:

```yaml theme={null}
    metrics: # Required: List of metrics to collect. Use mapping for renaming/type overrides.
      - planetscale_postgres_connection_state: postgres_connection_state
      - planetscale_edge_postgres_active_connections: edge_active_connections
      - planetscale_postgres_wal_size_bytes: postgres_wal_size
      - planetscale_pgbouncer_current_connections: pgbouncer_connections
```

Then, restart the Datadog Agent:

```bash theme={null}
$ sudo systemctl restart datadog-agent
```

## What's Next?

Now that you're sending Postgres metrics from PlanetScale to Datadog, take a look at our [full list](prometheus-metrics-postgres.md) and start building dashboards to monitor your PostgreSQL database performance, connection health, and resource utilization!

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
