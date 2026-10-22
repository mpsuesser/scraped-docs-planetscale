---
url: https://planetscale.com/docs/vitess/integrations/debezium-confluent
title: "Debezium Confluent"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Debezium connector on Confluent Cloud

> Run the Debezium connector for PlanetScale as a custom connector on Confluent Cloud.

The [Debezium connector for PlanetScale](https://planetscale.com/docs/vitess/integrations/debezium.md) runs on Confluent Cloud as a [custom connector](https://docs.confluent.io/cloud/current/connectors/bring-your-connector/overview.html): you upload the connector's Kafka Connect plugin archive once, then create connectors from it like any managed connector. This page walks through the upload and the connector wizard.

Before you start, you need:

* A Confluent Cloud cluster in a [region that supports custom connectors](https://docs.confluent.io/cloud/current/connectors/bring-your-connector/custom-connector-fands.html).
* The plugin archive `planetscale-debezium-connector-planetscale-<version>.zip` from the [releases page](https://github.com/planetscale/debezium-connector-planetscale/releases). Pick the release line as described on the [main page](https://planetscale.com/docs/vitess/integrations/debezium.md#pick-a-release).
* A PlanetScale password for the branch you want to capture.

## Upload the plugin and create the connector

Upload the plugin archive as a custom connector and configure it like a self-hosted Kafka Connect connector.

**Step 1**

In Confluent Cloud, go to **Connectors → Add plugin**. In the **Upload custom connector plugin** dialog choose **Create new plugin**, give it a name such as `PlanetScale 3.6.3.Final-r1`, pick the cloud provider of your Kafka cluster, and upload `planetscale-debezium-connector-planetscale-<version>.zip` (zip or jar, 250 MB maximum). Take a look at our connector releases [on GitHub](https://github.com/planetscale/debezium-connector-planetscale/releases).

![Confluent Cloud: Upload custom connector plugin dialog with the PlanetScale connector zip](https://mintcdn.com/planetscale-2/WPOFVUxRXVcpW_lU/images/assets/docs/integrations/debezium/confluent-1-upload-plugin.png?fit=max\&auto=format\&n=WPOFVUxRXVcpW_lU\&q=85\&s=7df1eecf204e61c44e0adad5ffe52bb6)

When a new connector release comes out, use **Add version to an existing plugin** on the same dialog instead of creating a new plugin.

**Step 2**

Click **Continue**. Confluent reads the plugin manifest from the archive and shows the plugin details: the version, the type (**Source**), the documentation link, and the connector classes it found.

![Confluent Cloud: connector plugin details listing the four connector classes found in the archive](https://mintcdn.com/planetscale-2/WPOFVUxRXVcpW_lU/images/assets/docs/integrations/debezium/confluent-3-connector-classes.png?fit=max\&auto=format\&n=WPOFVUxRXVcpW_lU\&q=85\&s=240f5f6a9eec0864467036c4a45e0257)

The archive contains four connector classes. Use **`com.planetscale.debezium.PlanetscaleConnector`**. The other three (`com.planetscale.labs.io.debezium.connector.vitess…`, `com.planetscale.labs.io.debezium.connector.mysql…`, and `io.debezium.connector.mysql.MySqlConnector`) are internal dependencies bundled with the plugin and are not meant to be run on their own.

Click **Create connector** to go straight to creating a connector from the new plugin.

You can also come back later: the plugin stays listed under **Connectors → Connector Plugins** (filter on **Custom**). From there, **Get started** creates a connector from it, and **Add version** uploads a newer release of the archive next to the existing one.

![Confluent Cloud: Connector Plugins page showing the PlanetScale custom plugin with Get started and Add version](https://mintcdn.com/planetscale-2/P43mbon7mBrqbRCV/images/assets/docs/integrations/debezium/confluent-8-plugin-list.png?fit=max\&auto=format\&n=P43mbon7mBrqbRCV\&q=85\&s=26341c15838be37feb93ca116b31eaca)

**Step 3**

The **Add connector** wizard opens on **Connector details**. Select the plugin version, keep the connector type on **Source**, and under **Select class** choose `com.planetscale.debezium.PlanetscaleConnector`.

Under **Kafka details**, the defaults work: the connector targets Java 17, so **JDK version** 17 or newer is fine, and any offered **Kafka version** from 3.6 up is supported.

![Confluent Cloud: Add connector wizard, connector details step with the PlanetScale class selected](https://mintcdn.com/planetscale-2/P43mbon7mBrqbRCV/images/assets/docs/integrations/debezium/confluent-4-connector-details.png?fit=max\&auto=format\&n=P43mbon7mBrqbRCV\&q=85\&s=313872d44f7905644966310cf009006b)

**Step 4**

On **Kafka access**, pick or create the API key the connector uses to write to your cluster.

**Step 5**

On **Configuration**, Confluent shows the connector's settings as a form. Fill in:

* **Topic prefix**: the prefix for the topics the connector creates.
* **Keyspace**: your PlanetScale database name.
* **Shard**: leave empty to capture all shards. Ignore the field's tooltip example of `"0"` for an unsharded keyspace: an unsharded Vitess keyspace's shard is named `-`, and a `0` here makes the connector fail to start.
* **vgtid** and **gtid**: leave at `current` to start streaming from the current position, after the initial snapshot.
* **Vitess database hostname** and **port**: the hostname from your PlanetScale connection string, and `443`. The port field is pre-filled with the connector's default `15991`. Change it to `443`, the only port PlanetScale listens on.
* **User** and **Password**: your PlanetScale password credentials. The password field is masked and stored encrypted by Confluent.

![Confluent Cloud: Add connector wizard, configuration step with the PlanetScale connection settings](https://mintcdn.com/planetscale-2/P43mbon7mBrqbRCV/images/assets/docs/integrations/debezium/confluent-5-configuration.png?fit=max\&auto=format\&n=P43mbon7mBrqbRCV\&q=85\&s=49fa6528d96394a85ae8f8f16e13ecdc)

Confluent manages the offset and schema-history storage for you; there is nothing to configure for them.

**Step 6**

On **Networking**, add your PlanetScale hostname **with port 443** (for example `aws.connect.psdb.cloud:443`) as a **connection endpoint**. Confluent allow-lists outbound traffic to it; without this the connector cannot reach PlanetScale. Confluent's [endpoint syntax](https://docs.confluent.io/cloud/current/connectors/bring-your-connector/custom-connector-qs.html) is `<hostname>:<port>`, so include the port.

![Confluent Cloud: Add connector wizard, networking step with the PlanetScale hostname as connection endpoint](https://mintcdn.com/planetscale-2/P43mbon7mBrqbRCV/images/assets/docs/integrations/debezium/confluent-6-networking.png?fit=max\&auto=format\&n=P43mbon7mBrqbRCV\&q=85\&s=e2cf8b700be38c7cf958d8ae9421854a)

**Step 7**

Choose a size on **Sizing** (one task is enough; the connector runs a single task), then **Review and launch**. The connector shows **Provisioning** for a minute or two and then **Running** with one task; the topics named `<topic prefix>.<database>.<table>` fill with the initial snapshot followed by live changes.

![Confluent Cloud: PlanetScale connector overview showing Running with one task running](https://mintcdn.com/planetscale-2/P43mbon7mBrqbRCV/images/assets/docs/integrations/debezium/confluent-7-running.png?fit=max\&auto=format\&n=P43mbon7mBrqbRCV\&q=85\&s=5f287d7e16c5567ed5e5fbf96ba17254)

Under **Topics**, filter on your topic prefix to see one topic per captured table. Without a **Table include list**, every table in the database gets a topic.

![Confluent Cloud: Topics page listing one topic per PlanetScale table under the connector's topic prefix](https://mintcdn.com/planetscale-2/P43mbon7mBrqbRCV/images/assets/docs/integrations/debezium/confluent-9-topics.png?fit=max\&auto=format\&n=P43mbon7mBrqbRCV\&q=85\&s=c84f399461c9e2ee61f58348e943e07b)

Your consumers subscribe to these topics; each message is a Debezium change event for one row.

## Related

> **[Debezium connector for PlanetScale](https://planetscale.com/docs/vitess/integrations/debezium.md)**

> **[Confluent custom connectors](https://docs.confluent.io/cloud/current/connectors/bring-your-connector/custom-connector-qs.html)**

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
