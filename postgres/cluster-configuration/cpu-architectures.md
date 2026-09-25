---
url: https://planetscale.com/docs/postgres/cluster-configuration/cpu-architectures
title: "Cpu Architectures"
description: ""
access_date: 2026-09-25T16:29:00.890Z
current_date: 2026-09-25T16:29:00.890Z
---

## Architecture Overview

### x86-64 (Intel/AMD)

The x86-64 architecture has been the dominant server architecture for decades. These processors offer:

- Mature ecosystem with extensive software optimization
- Wide compatibility with existing applications and tools
- High single-threaded performance
- Established performance benchmarks and tuning practices

### ARM64 (AWS Graviton)

ARM64 represents the next generation of server processors, with [AWS Graviton chips](https://aws.amazon.com/ec2/graviton/) specifically designed for cloud workloads:

- Custom silicon optimized for cloud applications
- Potential price-performance benefits, depending on the workload and cluster size
- Designs focused on energy efficiency
- Built on modern 64-bit ARM architecture

## Performance Comparison

### CPU Performance

On AWS Graviton and Google Axion instances, each cloud vCPU corresponds to a physical core. On x86 instances with simultaneous multithreading, two vCPUs share a physical core. Some PlanetScale sizes provide fractional vCPU allocations, so choosing ARM64 does not mean every cluster size receives a whole core.

- **Single-threaded**: Compare individual query latency on the available processors; architecture alone does not determine single-threaded performance
- **Concurrent workloads**: More physical cores at the same vCPU count can help with concurrent queries and parallel query workers
- **Memory bandwidth**: Depends on the processor and instance configuration, and can affect queries that scan large amounts of data

### PostgreSQL-Specific Performance

- **OLTP workloads**: ARM64 is a good default for ordinary reads and writes; test your workload to compare performance per dollar
- **Analytics workloads**: Compare your analytical queries on both architectures, including the parallelism and extension optimizations they use
- **Concurrent queries**: Test at production-like concurrency to compare throughput and latency
- **Background processes**: Additional cores can help background maintenance run alongside application queries

## Cost Considerations

### Infrastructure Costs

- **Instance prices**: Compare current prices for the sizes and region you need
- **Performance per dollar**: Compare the cost of configurations that meet your throughput and latency requirements
- **Storage**: Include storage charges and check [throughput limits](cluster-storage.md), which can vary by architecture and cluster size

### Total Cost of Ownership

- **Development overhead**: Client applications do not need to share the database server’s architecture
- **Migration effort**: Include the work required to migrate an existing database when evaluating potential savings
- **Scaling costs**: Compare the available sizes and prices as your workload grows

## Compatibility and Ecosystem

### Software Compatibility

- **PostgreSQL**: Full native support on both architectures ([PostgreSQL supported platforms](https://www.postgresql.org/docs/current/supported-platforms.html))
- **Extensions**: Check the [extensions available on PlanetScale](../extensions.md); implementations can use different optimizations on each architecture
- **Tools**: Client-side administration tools connect through the Postgres protocol and do not need to match the server architecture
- **Drivers**: An application on x86 can connect to an ARM64 database using the same Postgres driver and SQL

### Migration Considerations

- **Existing applications**: Changing the database server architecture alone does not require rebuilding applications that use standard PostgreSQL drivers
- **Binary extensions**: Code running inside Postgres must support the server architecture; use PlanetScale’s supported extensions
- **Performance tooling**: Some x86-specific optimization tools may not be available on ARM64

## Decision Matrix

| Consideration | x86-64 | ARM64 (Graviton) |
| --- | --- | --- |
| **Price-performance ratio** | Measure for your workload | Measure for your workload |
| **Single-threaded performance** | Depends on the processor | Depends on the processor |
| **Concurrent workloads** | vCPUs can share a physical core | One physical core per Graviton vCPU |
| **PostgreSQL extensions** | Check supported extensions | Check supported extensions |

**Choose x86-64 when:**

- Your workload benefits from x86-specific optimizations in supported extensions
- Tests show better performance for your CPU-heavy queries

**Choose ARM64 (Graviton) when:**

- It meets your performance requirements at a lower cost
- Running concurrent, multi-threaded workloads
- Starting new projects without legacy constraints

## Getting Started

Both architectures are available when creating new PostgreSQL databases in PlanetScale Postgres. You can specify your preferred architecture during the database creation process. For most new deployments doing ordinary reads and writes, ARM64 is a good default.

Consider running performance tests with your specific workload on both architectures to make the most informed decision for your use case.

You cannot change the CPU architecture of an existing cluster or mix architectures within its primary and replicas. New branches and backup restores retain the source architecture. To use a different architecture, create a new PlanetScale Postgres database and [migrate your data](../imports/postgres-imports.md). For help planning the migration, please [reach out to support](https://planetscale.com/contact?initial=support).

## CPU Architecture availability

Depending on your target region for your deployment, there may or may not be certain cluster size configurations that are available from the underlying provider. PlanetScale aims to have the most complete availability of resources for you to use and as is such may enable or disable certain configurations over time based on availability.

The most accurate source of this information is on the [Create a new database](https://app.planetscale.com/new) page and then selecting the desired region.

For customers of managed deployments, please [reach out to support](https://planetscale.com/contact?initial=support) for assistance in confirming availability for your deployment.

## Additional Resources

## AWS Graviton processors

## PostgreSQL Performance Tuning

## PostgreSQL supported platforms

## PlanetScale Postgres migration guides

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
