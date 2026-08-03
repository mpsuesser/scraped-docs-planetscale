---
url: https://planetscale.com/docs/vitess/integrations/debezium
title: "Debezium"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

The [Debezium Connector for PlanetScale](https://github.com/planetscale/debezium-connector-planetscale) is based on the [Debezium Vitess connector](https://debezium.io/documentation/reference/stable/connectors/vitess.html) and packages the PlanetScale-specific connector classes, patches, and runtime dependencies for Kafka Connect and Debezium Server.

This documentation shows you how to set up the Debezium connector for PlanetScale. This will allow you to get Debezium Server running on your machine, connect to PlanetScale, and send messages to a webhook endpoint.

Looking for the 2.x connector? See [Debezium connector for PlanetScale (archived)](https://planetscale.com/docs/vitess/integrations/debezium-archived).

## Install Java

1. First, you’ll need Java 21 installed on your machine. You can find this at [https://www.oracle.com/java/technologies/downloads/](https://www.oracle.com/java/technologies/downloads/).

## Running standalone in Debezium Server

[Debezium Server](https://debezium.io/documentation/reference/stable/operations/debezium-server.html) is a standalone application that can test a Debezium connector end-to-end by hosting the Debezium core as an in-process library and pass data from the source to the sink.

### Configure the Debezium connector for PlanetScale

Edit `server/ps.properties` with your configuration.

In this example config, we are going to have the sink send HTTP requests to `webhook.site`.

Go to [`http://webhook.site`](http://webhook.site/) to get your own endpoint.

Place the sample config below in `server/ps.properties`, replacing the following placeholders:

- `<webhook>` with your webhook.site endpoint.
- `<planetscale-database-name>` with your PlanetScale database name.
- `<planetscale-hostname>` with your PlanetScale connection string hostname.
- `<planetscale-username>` with your PlanetScale connection string username.
- `<planetscale-password>` with your PlanetScale connection string password.

```ini
debezium.format.value=json

debezium.sink.type=http
debezium.sink.http.url=<webhook>

debezium.source.schema.history.internal=io.debezium.storage.file.history.FileSchemaHistory
debezium.source.schema.history.internal.file.filename=data/schema_history.dat

debezium.source.offset.storage.file.filename=data/offsets.dat
debezium.source.offset.flush.interval.ms=0

debezium.source.database.hostname=<planetscale-hostname>
debezium.source.database.port=443
debezium.source.database.user=<planetscale-username>
debezium.source.database.password=<planetscale-password>

debezium.source.vitess.keyspace=<planetscale-database-name>

debezium.source.connector.class=com.planetscale.debezium.PlanetscaleConnector
debezium.source.topic.prefix=connector-test

debezium.tasks.max=1
```

## Run it

Once the config is set, you can start it by running `make -C server run`

Any existing rows in any table of `<planetscale-database-name>` will show up as events in your `webhook.site` endpoint. Adding/modifying/deleting rows will also show up as events in your endpoint.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
