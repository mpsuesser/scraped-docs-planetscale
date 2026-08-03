---
url: https://planetscale.com/docs/postgres/monitoring/prometheus-postgres
title: "Prometheus Postgres"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Prometheus integration for PlanetScale Postgres

> PlanetScale Postgres exposes Prometheus-compatible metrics endpoints for scraping metrics about your database branches. This, along with our API-driven service discovery, allows you to automatically get in-depth information about all of the Postgres databases in your organization.

**Platform availability:** [Vitess](../../vitess/integrations/prometheus.md) and Postgres

In order to collect and store these, you will need to use Prometheus or a Prometheus-compatible metrics engine (such as VictoriaMetrics) that is capable of using the [HTTP SD](https://prometheus.io/docs/prometheus/latest/http_sd/) protocol.

## Prerequisites

This document assumes you'll be configuring a Prometheus 3.x instance via a configuration file running on your local machine.

If you are using managed Prometheus via AWS, GCP or another provider, you will have to deploy Prometheus to scrape and forward metrics via `remote_write`, as these services do not support scraping metrics.

## Getting Started

First, provision a new PlanetScale [Service token](../../api/reference/service-tokens.md) in your Organization settings. Make sure to save the ID and token, as they will not be visible after they've been generated.

When that's created, grant the token `read_metrics_endpoints` permissions and click "Save permissions". Your token should have the necessary permissions to access Postgres metrics endpoints.

## Configuring Prometheus

Now that you have a Service Token, you can add a scrape configuration for your PlanetScale organization. A minimal Prometheus configuration should look like the following:

```yaml theme={null}
scrape_configs:
  - job_name: "${ORG}-postgres"
    http_sd_configs:
    - url: https://api.planetscale.com/v1/organizations/${ORG}/metrics
      authorization:
        type: "token"
        credentials: "${TOKEN_ID}:${TOKEN}"
      refresh_interval: 10m
```

Fill in your organization name in the `job_name` and `url`, and place the Service Token and ID that you created in the previous step for the credentials.

Save this file to `prometheus.yml` in your working directory.

## Start Prometheus

Run Prometheus pointed at this configuration file:

```bash theme={null}
$ prometheus --config.file=prometheus.yml
```

By default, Prometheus will listen at `0.0.0.0:9090`, which means you can access it in your browser at [http://127.0.0.1:9090](http://127.0.0.1:9090/).

### Validating Service Discovery

First, make sure that Prometheus is properly querying the PlanetScale API for the right branches. If you go to `http://127.0.0.1:9090/service-discovery` you should see the job that you created earlier, with all of your Postgres branches listed under `Discovered labels`.

You can confirm that this matches what's in your organization by using the PlanetScale CLI:

```bash theme={null}
$ pscale branch list your-postgres-database --org your-org
```

Now, if you go to your list of targets you should see each Postgres branch as an Endpoint with properly configured scraping targets.

## Querying Prometheus

Now that you're collecting metrics for your Postgres branches, the [reference guide](prometheus-metrics-postgres.md) has a list of everything that PlanetScale exports.

Here are some example queries you can run:

### Database Connection State

To see the current connection states across your Postgres instances:

```sql theme={null}
planetscale_postgres_connection_state{planetscale_database_branch_id="your-branch-id"}
```

### Active Edge Connections

To monitor active connections at the edge:

```sql theme={null}
planetscale_edge_postgres_active_connections{planetscale_database_branch_id="your-branch-id"}
```

### WAL Size Monitoring

To track WAL size in bytes:

```sql theme={null}
planetscale_postgres_wal_size_bytes{planetscale_database_branch_id="your-branch-id"}
```

### PgBouncer Connection Pools

To monitor PgBouncer connection pool status:

```sql theme={null}
planetscale_pgbouncer_current_connections{planetscale_database_branch_id="your-branch-id"}
```

### Resource Utilization

To check CPU utilization across your database pods:

```sql theme={null}
planetscale_pods_cpu_util_percentages{planetscale_database_branch_id="your-branch-id"}
```

Make sure the graph is set to stacked for multi-series metrics to get the best visualization.

## Next Steps

If you keep this Prometheus instance running, it will collect metrics every 30 seconds, and refresh the list of branches every 10 minutes.

For more information, see:

* [Postgres Metrics reference](prometheus-metrics-postgres.md) for a complete list of metrics PlanetScale exposes
* [Sending metrics to Datadog](prometheus-metrics-datadog-postgres.md) tutorial for using the Datadog agent to collect these metrics
* [Grafana dashboards](../../vitess/tutorials/prometheus-metrics-grafana.md) for visualizing these metrics (instructions applicable for Postgres metrics along with our [Postgres dashboard template](https://github.com/planetscale/grafana-dashboard/blob/main/postgres.json))

## Troubleshooting

### Service Discovery Issues

If you're not seeing your Postgres branches in the service discovery:

1. Verify your service token has the `read_metrics_endpoints` permission
2. Check that your organization name in the URL matches exactly
3. Ensure your service token credentials are correctly formatted as `${TOKEN_ID}:${TOKEN}`

### Missing Metrics

If specific metrics aren't appearing:

1. Confirm the branch is active and healthy
2. Check the scrape interval - some metrics may take time to appear
3. Verify the metric names against our [metrics reference](prometheus-metrics-postgres.md)

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
