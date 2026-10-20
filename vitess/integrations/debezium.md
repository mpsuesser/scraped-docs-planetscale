---
url: https://planetscale.com/docs/vitess/integrations/debezium
title: "Debezium"
description: ""
access_date: 2026-10-20T16:23:25.828Z
current_date: 2026-10-20T16:23:25.828Z
---

The [Debezium connector for PlanetScale](https://github.com/planetscale/debezium-connector-planetscale) is based on the [Debezium Vitess connector](https://debezium.io/documentation/reference/stable/connectors/vitess.html) and packages the PlanetScale-specific connector classes, patches, and runtime dependencies for Kafka Connect and Debezium Server. It streams change events from a PlanetScale Vitess database through [VStream](https://vitess.io/docs/concepts/vstream/), including the initial copy of existing rows.

This page covers which connector release to pick, how to connect it to PlanetScale, the deployment options we test, and what each release is verified against.

Looking for the 2.x connector? See [Debezium connector for PlanetScale (archived)](https://planetscale.com/docs/vitess/integrations/debezium-archived).

## Pick a release

We publish one connector release line per upstream Debezium minor version, so you can match the Debezium version already running in your environment. Each line tracks the latest upstream patch release of that minor. Release tags look like `v<debezium version>-r<N>`, where `r<N>` counts PlanetScale releases on top of the same upstream version.

| Debezium line | Connector release | Debezium Server | Kafka Connect / Confluent |
| --- | --- | --- | --- |
| 3.2 | [v3.2.7.Final-r2](https://github.com/planetscale/debezium-connector-planetscale/releases/tag/v3.2.7.Final-r2) | ✅ | ✅ |
| 3.3 | [v3.3.2.Final-r1](https://github.com/planetscale/debezium-connector-planetscale/releases/tag/v3.3.2.Final-r1) | ✅ | ✅ |
| 3.4 | [v3.4.3.Final-r1](https://github.com/planetscale/debezium-connector-planetscale/releases/tag/v3.4.3.Final-r1) | ✅ | ✅ |
| 3.5 | [v3.5.2.Final-r1](https://github.com/planetscale/debezium-connector-planetscale/releases/tag/v3.5.2.Final-r1) | ✅ | ✅ |
| 3.6 | [v3.6.3.Final-r1](https://github.com/planetscale/debezium-connector-planetscale/releases/tag/v3.6.3.Final-r1) | ✅ | ✅ |

If you have no existing Debezium deployment, start with the **3.6 line**, the newest one available.

## Download the connector

Every release on the [releases page](https://github.com/planetscale/debezium-connector-planetscale/releases) ships the same set of files:

| File | Use it for |
| --- | --- |
| `planetscale-debezium-adapter-<version>.jar` | Debezium Server: drop into the server’s `lib/` directory |
| `planetscale-debezium-connector-planetscale-<version>.zip` | Kafka Connect and Confluent Cloud: the connector plugin archive |
| `SHA256SUMS.txt` | Checksums for all files |
| `*.sigstore.json`, `release.spdx.json` | Sigstore signatures and an SBOM for supply-chain verification |

Verify what you downloaded before deploying it:

```shellscript
# in the directory with the downloaded files (on macOS use \`shasum -a 256 -c -\`)
grep -E '\.(jar|zip)$' SHA256SUMS.txt | sha256sum -c -
```

## Connection settings

These settings are the same for every deployment option. In Kafka Connect and Confluent they are plain connector properties; in Debezium Server they carry the `debezium.source.` prefix.

| Property | Value |
| --- | --- |
| `connector.class` | `com.planetscale.debezium.PlanetscaleConnector` |
| `database.hostname` | The hostname from your PlanetScale connection string, for example `aws.connect.psdb.cloud` |
| `database.port` | `443` |
| `database.user` / `database.password` | A [PlanetScale password](../connecting/connection-strings.md) for the branch. The connector connects over TLS automatically. |
| `vitess.keyspace` | Your PlanetScale database name |
| `vitess.tablet.type` | `MASTER` to stream from the primary, or `REPLICA` to stream from a replica |
| `table.include.list` | Comma-separated `<database>.<table>` names to capture. Omit to capture every table. |
| `topic.prefix` | Prefix for the Kafka topics (or event names): events for a table land in `<prefix>.<database>.<table>` |
| `snapshot.mode` | `initial` (default) copies existing rows first, then streams; `never` streams only new changes |

Notes:

- The connector talks to PlanetScale over gRPC on port 443 with TLS. Outbound access to your connection-string hostname on port 443 is all it needs.
- On a sharded PlanetScale database the connector streams all shards; leave `vitess.shard` unset.
- Keep `tasks.max=1`. The Vitess connector runs a single task per keyspace.

## Deploy

We test every release in the deployment options below.

### Debezium Server with the official image

The quickest way to run the connector standalone. Mount the connector jar into the official Debezium Server image for the matching line and mount your configuration.

```ini
# application.properties
debezium.format.value=json

debezium.sink.type=http
debezium.sink.http.url=<sink url>

debezium.source.connector.class=com.planetscale.debezium.PlanetscaleConnector
debezium.source.database.hostname=<planetscale hostname>
debezium.source.database.port=443
debezium.source.database.user=<planetscale username>
debezium.source.database.password=<planetscale password>
debezium.source.vitess.keyspace=<planetscale database name>
debezium.source.vitess.tablet.type=MASTER
debezium.source.topic.prefix=connector-test

debezium.source.offset.storage=org.apache.kafka.connect.storage.FileOffsetBackingStore
debezium.source.offset.storage.file.filename=data/offsets.dat
debezium.source.offset.flush.interval.ms=0
debezium.source.schema.history.internal=io.debezium.storage.file.history.FileSchemaHistory
debezium.source.schema.history.internal.file.filename=data/schema_history.dat
```

```shellscript
# pick the Debezium Server image that matches the connector line, e.g. 3.6.3.Final for v3.6.3.Final-r1
docker run --rm \
  -v "$PWD/planetscale-debezium-adapter-3.6.3.Final-r1.jar:/debezium/lib/planetscale-debezium-adapter-3.6.3.Final-r1.jar:ro" \
  -v "$PWD/application.properties:/debezium/config/application.properties:ro" \
  -v "$PWD/data:/debezium/data" \
  quay.io/debezium/server:3.6.3.Final
```

Mount `data/` to persist offsets and schema history across restarts. Existing rows arrive as events first (the snapshot), followed by changes as they happen.

### Debezium Server on a VM or your laptop

Download the Debezium Server distribution for the same line from Maven Central, unpack it, copy the connector jar into `lib/` and your configuration into `config/application.properties`, then start it with `run.sh`. Requires Java 21.

```shellscript
V=3.6.3.Final
curl -fLO https://repo1.maven.org/maven2/io/debezium/debezium-server-dist/$V/debezium-server-dist-$V.tar.gz
tar -xzf debezium-server-dist-$V.tar.gz
cp planetscale-debezium-adapter-$V-r1.jar debezium-server/lib/
cp application.properties debezium-server/config/
cd debezium-server && ./run.sh
```

The repository’s [`server/`](https://github.com/planetscale/debezium-connector-planetscale/tree/main/server) helper (`make -C server run`) automates these steps for a local build.

### Self-hosted Kafka Connect

Unzip the plugin archive into a directory on the worker’s `plugin.path`, restart the worker, and register the connector:

```shellscript
unzip planetscale-debezium-connector-planetscale-3.6.3.Final-r1.zip -d /opt/connect/plugins/
```

```json
{
  "name": "planetscale",
  "config": {
    "connector.class": "com.planetscale.debezium.PlanetscaleConnector",
    "tasks.max": "1",
    "database.hostname": "<planetscale hostname>",
    "database.port": "443",
    "database.user": "<planetscale username>",
    "database.password": "<planetscale password>",
    "vitess.keyspace": "<planetscale database name>",
    "vitess.tablet.type": "MASTER",
    "topic.prefix": "connector-test",
    "table.include.list": "<database>.<table>",
    "schema.history.internal.kafka.bootstrap.servers": "kafka:9092",
    "schema.history.internal.kafka.topic": "schema-changes.planetscale"
  }
}
```

```shellscript
curl -s -X POST -H 'Content-Type: application/json' --data @planetscale.json http://connect:8083/connectors
curl -s http://connect:8083/connectors/planetscale/status
```

Both the connector and its task should report `RUNNING`, and topics named `<topic.prefix>.<database>.<table>` appear as events arrive.

### Confluent Cloud custom connector

Upload the plugin archive as a custom connector plugin, then create a connector from it through the Confluent wizard: pick the `com.planetscale.debezium.PlanetscaleConnector` class, fill in the PlanetScale connection settings (port `443`, Shard left empty), and allow-list `<your PlanetScale hostname>:443` as a connection endpoint. The full walkthrough with screenshots is on its own page.

## Debezium connector on Confluent Cloud

## What each release is tested against

**Build-time tests** (run in CI on every change):

- Value conversion for the Vitess types that differ from MySQL defaults: `BIT` columns, `TIMESTAMP` / `DATETIME` precision modes and zero dates, unsigned `BIGINT` handling, and spatial (`GEOMETRY`) columns.
- Connection handling: TLS to PlanetScale, mutual TLS, and the gRPC authentication interceptor that carries the PlanetScale credentials.
- Connector configuration validation and start-up, embedded-engine start-up, and the Kafka Connect plugin service registration.
- An integration test against a Vitess cluster in a container that exercises VStream end to end.

**Release validation** against a live PlanetScale database, using the downloaded release files (checksums verified against `SHA256SUMS.txt`):

- **Debezium Server** on the official `quay.io/debezium/server` image of the same line, with the connector jar in `lib/`.
- **Debezium Server distribution** (the Maven Central tarball) on a plain JVM, the way a VM or laptop deployment runs it.
- **Self-hosted Kafka Connect** with the plugin archive, connector and task reaching `RUNNING`.
- **Confluent Cloud** as a custom connector, uploaded from the plugin archive.

Each run captures a fixed set of tables and checks that the initial copy and the following inserts, updates and deletes all arrive at the sink with the expected content: a plain table, a table of `TIMESTAMP` / `DATETIME` columns including zero-date values, and a table of `BIT` columns.

## Known limitations

- **Zero dates**: a `0000-00-00 00:00:00` value is emitted as `null`, with a warning in the log. This follows Debezium’s handling of invalid dates.
- **`bigint.unsigned.handling.mode`**: the Vitess connector supports `precise` and `long` only. If your configuration sets `string`, the connector logs a warning and falls back to `precise`.
- **Topic naming**: events for a table go to `<topic.prefix>.<database>.<table>`. Vitess shard names are not part of the topic name.

## Upgrading from the 2.x connector

The 2.x connector (archived) was a fork of the Debezium 2.4 Vitess connector shipped as a single jar with dependencies. The 3.x lines are a different artifact: the connector class name changes from `io.debezium.connector.planetscale.PlanetScaleConnector` to `com.planetscale.debezium.PlanetscaleConnector`, the Kafka Connect plugin is a zip archive, and the Debezium Server jar must match the server’s Debezium line.

Offsets carry over. The 3.x connector reads the offsets written by the 2.x connector (same partition key and the same `vgtid` offset entry), skips the snapshot, and resumes the VStream from the last recorded VGTID. We verify this for both runtimes: the archived connector on Debezium Server 2.4.1 followed by the 3.6 line on the same offsets file, and the archived connector on a Kafka Connect worker followed by the 3.6 line registered under the same connector name against the same offsets topic. In both cases no rows are re-sent and streaming continues from the stored position.

To keep the offsets matched after the upgrade:

- Keep the same connector name (`name` in Kafka Connect, `debezium.source.name` in Debezium Server) and the same `topic.prefix`. Both are part of the key under which offsets are stored.
- Point the new connector at the same offset storage: the same `offset.storage.file.filename` in Debezium Server, or the same offsets topic in Kafka Connect.
- Change `connector.class` to the new class name.

Columns of type `BIT` were not supported by the 2.x connector and were dropped from events with an `unknown column type` warning. The 3.x lines emit them, so tables with `BIT` columns gain those fields after the upgrade.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
