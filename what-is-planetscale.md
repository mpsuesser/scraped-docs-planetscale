---
url: https://planetscale.com/docs/what-is-planetscale
title: "What Is Planetscale"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# What is PlanetScale?

> A fully managed relational database platform for Postgres and Vitess, built for scale, performance, and reliability.

export const VimeoEmbed = ({id, title}) => {
  return <Frame>
      <iframe src={`https://player.vimeo.com/video/${id}?dnt=true`} title={title} className="aspect-video w-full" allow="autoplay; fullscreen; picture-in-picture" />
    </Frame>;
};

PlanetScale is a fully managed relational database platform for [Vitess](vitess.md) and [Postgres](postgres.md), bringing you scale, performance, and reliability — without sacrificing developer experience.

We don't call ourselves the world's fastest database for nothing. Benchmarks for both [Vitess](https://planetscale.com/benchmarks/vitess) and [Postgres](https://planetscale.com/blog/benchmarking-postgres) show PlanetScale in the lead over and over again for both queries per second and latency.

With PlanetScale, you get blazing fast performance with our [locally-attached NVMe drives](metal.md), high availability 3 node clusters (1 primary and 2 replicas by default), automated failovers, connection pooling, online fully-managed version upgrades, and more.

Our Vitess product offers unlimited scalability through explicit horizontal sharding. Vitess was [created at YouTube in 2010](https://vitess.io/overview/history/#:~:text=Vitess%20was%20created%20in%202010,exceed%20the%20database's%20serving%20capacity.) to solve the scaling issues they faced with their massive MySQL database. Vitess was later donated to the CNCF and continues to scale massive companies like [Slack](https://slack.engineering/scaling-datastores-at-slack-with-vitess/), [GitHub](https://github.blog/2021-09-27-partitioning-githubs-relational-databases-scale/), and more.

The team building PlanetScale is made up of passionate industry experts who have spent decades working on databases for some of the web's largest companies. Our team has directly felt the pain of overly-complicated, unintuitive database tools and came to PlanetScale to build the future of databases — the database they wished they had at their previous companies.

## PlanetScale features

The fastest way to understand how PlanetScale is changing the database landscape is to take a peek inside the product. The following features create a powerful developer experience that enables teams to develop quickly and confidently.

### PlanetScale Metal

When you deploy a PlanetScale database, you can choose from network-attached storage or Metal — locally-attached NVMe SSD drives. These blazing fast NVMe drives unlock unlimited IOPS, ultra-low latencies, and the [highest throughput](https://planetscale.com/blog/benchmarking-postgres) for your workloads while still running in the cloud (AWS or GCP).

### Non-blocking schema changes

With most businesses now operating online, downtime and maintenance windows are no longer acceptable. Not only does downtime hurt customer experience and trust, but even a small amount of downtime can result in thousands to millions of dollars lost for companies.

Our [non-blocking schema change workflow](vitess/schema-changes.md) for Vitess means that you'll never experience costly table locking or downtime when running schema changes. This is a fundamental piece of PlanetScale and something that we think everyone should have access to, so there's no additional configuration required. Zero-downtime schema changes are baked into the product.

### Branching workflow

Our [branching workflow](vitess/schema-changes/branching.md) paired with [safe migrations](vitess/schema-changes/safe-migrations.md) is what enables non-blocking schema changes on your production Vitess database. Instead of applying schema changes directly to your production database, we let you create branches, which are essentially copies of your database. When you create a new branch off of production, you have an isolated copy of your database that you can use for development to make schema changes.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/what-is-planetscale/branching-diagram.png?fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=d54f0954cf67d1b8586eea998ae20c98" alt="Branching workflow diagram - Create dev branch off of main, make schema changes, make deploy request, resolve schema conflicts, test, deploy to main" width="1234" height="652" data-path="images/assets/docs/concepts/what-is-planetscale/branching-diagram.png" />
</Frame>

Development branches can serve as your staging environment, so you don't have to worry about spinning up a new testing database and constantly syncing it with production. We handle all of that for you.

Once you're ready to deploy schema changes from your development branch to production, you [open a deploy request](vitess/schema-changes/deploy-requests.md). The deploy request allows your team to view a diff of the schema changes being made, comment, and approve before deploying the change to production.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/GA0k5H-MolPvBjDk/images/assets/docs/concepts/what-is-planetscale/deploy-request.png?fit=max&auto=format&n=GA0k5H-MolPvBjDk&q=85&s=b1048b95a92ac1c91750bcfca9b99a60" alt="Example of a deploy request showing comments, approval, and deployment" width="3430" height="2776" data-path="images/assets/docs/concepts/what-is-planetscale/deploy-request.png" />
</Frame>

### Revert a schema change

The final piece of the non-blocking schema change workflow is the ability to [revert a recently deployed schema change](vitess/schema-changes/deploy-requests.md#revert-a-schema-change) without losing any data that was written since deploying.

<VimeoEmbed id="830571822" title="Revert a schema change" />

Despite all the safeguards we put in place, accidents can happen. If someone on your team deploys a schema change, only to realize afterward that it adversely affected the application, you can simply revert it in the PlanetScale dashboard with the click of a button. Perhaps the most impressive part is that when you revert, you won't lose any data that was written to your database during the time the updated schema was live. We keep track of it and apply it back to the original schema once you revert.

No more fumbling around with snapshots or backups and restores. Just revert.

### Scale with sharding + unlimited connections

With Vitess under the hood, we're able to offer horizontal scaling via sharding with minimal application changes.

PlanetScale allows you to break up a monolithic database and partition the data across several databases. This [reduces the load on a single database](https://planetscale.com/blog/one-million-queries-per-second-with-mysql) by distributing it across several. Sharding can easily become a convoluted and hard-to-manage scenario, but because of our underlying architecture, we're able to keep this sharding logic largely out of the application. So, from the application's perspective, there only exists one database.

Another scenario that companies with massive databases often run into is connection limits due to MySQL. With PlanetScale, we can support [nearly infinite connections](https://planetscale.com/blog/one-million-connections). Vitess offers built-in [connection pooling](https://vitess.io/reference/features/connection-pools/), and we've built our own [edge infrastructure](https://planetscale.com/blog/introducing-the-planetscale-serverless-driver-for-javascript) into PlanetScale to ensure connection limits are never an issue.

We generally recommend exploring horizontal sharding when your database exceeds 250 GB of data and you are beginning to feel some of the [pains associated with large scale](https://planetscale.com/blog/how-to-scale-your-database-and-when-to-shard-mysql). [Sharding](vitess/sharding/sharding-quickstart.md) is offered on our Base plan. If you need assistance with setting up horizontal sharding, migrating to PlanetScale, or want enterprise-level SLAs, we offer this through our [Enterprise plan](https://planetscale.com/enterprise) option. [Please reach out](https://planetscale.com/contact) for more information.

### Insights

[PlanetScale Insights](vitess/monitoring/query-insights.md) is our in-dashboard query performance analytics tool. What's unique about Insights is that you can track performance down to the individual query level.

<VimeoEmbed id="830571854" title="PlanetScale Insights" />

At a glance, the interactive graph shows you query latency, queries per second, rows read, and rows written charted against time. You'll also see any deploy requests on the graph, so you can quickly see the impact of those changes.

If you notice your application is running slower than it should or you want to do a deep dive on your bill, you can come to the Insights dashboard and drill down at the individual query level to see:

* number of times a query has run
* total time the query has run
* time per query
* rows read, affected, and returned

### No downtime import tool

We understand changing database providers can be a pain, from dealing with downtime to complicated dumps and restores and endless compatibility issues.

We built a [database import tool](vitess/imports/database-imports.md) to make importing to Vitess as pain-free as possible.

With our import tool, you can connect your internet-accessible database to PlanetScale and begin the import process. During the import, your production database remains live, and both your PlanetScale and production databases are continuously synced. This means that as new or updated data hits your production database, PlanetScale will pull it in as long as the connection remains open. Once you're ready to do the swap, the cutover happens in an instant. No downtime and no data loss.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/89X51wIXzJwNfurq/images/assets/docs/imports/import-workflows/validate-import-workflow.png?fit=max&auto=format&n=89X51wIXzJwNfurq&q=85&s=c6cb5e1e8da03f19ee49680960351b87" alt="Step 3 of database import - Primary mode" width="2670" height="2950" data-path="images/assets/docs/imports/import-workflows/validate-import-workflow.png" />
</Frame>

### Connect

A common task companies need to handle is extracting data out of their database for transformation and analysis.

[PlanetScale Connect](vitess/integrations/airbyte.md) provides you with an easy way to perform ELT. You can connect your PlanetScale database to Airbyte or Stitch, and select the destination from there. Both platforms support several data storage destinations, such as Snowflake, Google Big Query, and more.

### CLI

Nearly every action you can take in the PlanetScale dashboard can also be done with our [`pscale` CLI](cli.md).

With commands for branching, deploy requests, backups, service tokens, and more, the CLI allows teams to work quickly and efficiently. You can use the CLI to extend PlanetScale into your own DevOps workflow with [GitHub Actions](https://planetscale.com/blog/using-the-planetscale-cli-with-github-actions-workflows), [AWS CodeBuild](https://planetscale.com/blog/build-a-multi-stage-pipeline-with-planetscale-and-aws), and more.

### API

Like the CLI, you can programmatically interact with PlanetScale using our [API](api/planetscale-api-oauth-applications.md).

The API is useful for building PlanetScale into other developer tooling for faster development workflows. For example, you can programmatically create and delete database branches, open and merge deploy requests, and more.

See the [PlanetScale API reference](api/reference/getting-started-with-planetscale-api.md) for more information.

### Replicas

Every production PlanetScale branch comes with two [replicas](vitess/scaling/replicas.md). Replicas are read-only copies of your database that can be used to offload read traffic from your primary. With global replica credentials, you can have one credential that will automatically route queries to your branch's replicas and read-only regions.

### Read-only regions

Spin up [read-only regions](vitess/scaling/read-only-regions.md) for Vitess with the click of a button. For globally distributed applications, read-only regions allow you to place a copy of your data close to your users.

To query your read-only region, create [a replica credential](vitess/scaling/replicas.md) for your database. Replica queries will be automatically routed to the nearest read-only region or one of the branch's replicas, whichever has the lowest latency available.

## Get in touch

Want to learn more about PlanetScale and how it can help your business prevent downtime and improve development speed?

[Reach out to learn more or schedule a demo](https://planetscale.com/contact), and we'll be in touch shortly.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
