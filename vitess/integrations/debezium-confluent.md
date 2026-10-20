---
url: https://planetscale.com/docs/vitess/integrations/debezium-confluent
title: "Debezium Confluent"
description: ""
access_date: 2026-10-20T16:23:25.828Z
current_date: 2026-10-20T16:23:25.828Z
---

The [Debezium connector for PlanetScale](debezium.md) runs on Confluent Cloud as a [custom connector](https://docs.confluent.io/cloud/current/connectors/bring-your-connector/overview.html): you upload the connector’s Kafka Connect plugin archive once, then create connectors from it like any managed connector. This page walks through the upload and the connector wizard.

Before you start, you need:

- A Confluent Cloud cluster in a [region that supports custom connectors](https://docs.confluent.io/cloud/current/connectors/bring-your-connector/custom-connector-fands.html).
- The plugin archive `planetscale-debezium-connector-planetscale-<version>.zip` from the [releases page](https://github.com/planetscale/debezium-connector-planetscale/releases). Pick the release line as described on the [main page](debezium.md#pick-a-release).
- A PlanetScale password for the branch you want to capture.

## Upload the plugin and create the connector

Upload the plugin archive as a custom connector and configure it like a self-hosted Kafka Connect connector.

## Related

## Debezium connector for PlanetScale

## Confluent custom connectors

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
