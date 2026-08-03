---
url: https://planetscale.com/docs/vitess/integrations/prometheus
title: "Prometheus"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Prometheus

> PlanetScale exposes Prometheus-compatible metrics endpoints for scraping metrics about your database branches. This, along with our API-driven service discovery, allow you to automatically get in-depth information about all of the databases in your organization.

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

<PlatformAvailability current="vitess" postgres="/postgres/monitoring/prometheus-postgres" />

In order to collect and store these, you will need to use Prometheus or a Prometheus-compatible metrics engine (such as VictoriaMetrics) that is capable of using the [HTTP SD](https://prometheus.io/docs/prometheus/latest/http_sd/) protocol.

## Prerequisites

This document assumes we'll be configuring a Prometheus 3.x instance via a configuration file running on our local machine.

If you are using managed Prometheus via AWS, GCP or another provider, you will have to deploy Prometheus to scrape and forward metrics via `remote_write`, as these services do not support scraping metrics.

## Getting Started

First, provision a new PlanetScale [Service token](../../api/reference/service-tokens.md) in your Organization settings. Make sure to save the ID and token, as they will not be visible after they've been generated.

When that's created, grant the token `read_metrics_endpoints` permissions and click "Save permissions". Your token should look like the following:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metrics-service-token-configuration.png?fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=7154912fcc404c404843cdbed160db0f" alt="Service Token configuration for Metrics Exporting" width="1348" height="1136" data-path="images/metrics-service-token-configuration.png" />
</Frame>

## Configuring Prometheus

Now that we have a Service Token, we can add a scrape configuration for your PlanetScale organization. A minimal Prometheus configuration should look like the following:

```yaml theme={null}
scrape_configs:
  - job_name: "${ORG}"
    http_sd_configs:
    - url: https://api.planetscale.com/v1/organizations/${ORG}/metrics
      authorization:
        type: "token"
        credentials: "${TOKEN_ID}:${TOKEN}"
      refresh_interval: 10m
```

Fill in your organization name in the `job_name` and `url`, and place the Service Token and ID that we created in the previous step for the credentials.

Save this file to `prometheus.yml` in your working directory.

## Start Prometheus

Run Prometheus pointed at this configuration file:

```bash theme={null}
$ prometheus --config.file=prometheus.yml
```

By default, Prometheus will listen at `0.0.0.0:9090`, which means you can access it in your browser at [http://127.0.0.1:9090](http://127.0.0.1:9090).

### Validating Service Discovery

First, let's make sure that Prometheus is properly querying the PlanetScale API for the right branches. If you go to `http://127.0.0.1:9090/service-discovery` you should see the job that we created earlier, with all of your branches listed under `Discovered labels`. In this example, our organization is called `nick`, so it looks like the following:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metrics-prometheus-targets.png?fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=96f64a28bdbaac27e83310a751941169" alt="Prometheus Target List" width="2886" height="1456" data-path="images/metrics-prometheus-targets.png" />
</Frame>

Here, I have two branches that have been discovered. I can confirm that this matches what's in my organization:

```bash theme={null}
$ pscale branch list test --org nick
  ID             NAME         PARENT BRANCH   REGION    PRODUCTION   SAFE MIGRATIONS   READY   CREATED AT    UPDATED AT
 -------------- ------------ --------------- --------- ------------ ----------------- ------- ------------- ---------------
  7wxuxewx4l0p   main         n/a             us-east   Yes          Yes               Yes     2 years ago   7 minutes ago
  6o0rr27785fl   partitions   main            us-east   No           No                Yes     1 month ago   9 minutes ago
```

Now, if I go to my list of targets I should see each branch as an Endpoint:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metrics-prometheus-endpoints.png?fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=6830ae6e1a43d6667ccde7f6784d8e5c" alt="Prometheus Endpoint List" width="2384" height="660" data-path="images/metrics-prometheus-endpoints.png" />
</Frame>

This screenshot shows that they're being correctly scraped, and I can start to query my Prometheus instance.

## Querying Prometheus

Now that we're collecting metrics for my branches, our [reference guide](prometheus-metrics.md) has a list of everything that we export. If I want to see how many `vtgate` pods are running per AZ for my branch, I can query:

```
planetscale_vtgate_total_pods{planetscale_database_branch_id="7wxuxewx4l0p"}
```

Make sure the graph is set to stacked, and it should look like this:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/qp-ZKr_vKb0iLpeb/images/metrics-prometheus-querying.png?fit=max&auto=format&n=qp-ZKr_vKb0iLpeb&q=85&s=5271cb0215290d804c5c71d29cea7759" alt="Querying Prometheus for VTGate Count" width="2710" height="2422" data-path="images/metrics-prometheus-querying.png" />
</Frame>

## Next Steps

If you keep this Prometheus instance running, it will collect metrics every 30 seconds, and refresh the list of branches every 10 minutes.

For more information, see:

* [Metrics reference](prometheus-metrics.md) for a list of metrics we expose
* [Grafana and Prometheus](../tutorials/prometheus-metrics-grafana.md) tutorial for using PlanetScale's provided dashboard to visualize these metrics in Grafana.
* [Sending metrics to New Relic](../tutorials/prometheus-metrics-newrelic.md) tutorial for using Prometheus to forward metrics to New Relic.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
