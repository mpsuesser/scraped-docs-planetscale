---
url: https://planetscale.com/docs/postgres/postgres-architecture
title: "Postgres Architecture"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale Postgres architecture

> PlanetScale Postgres is a managed PostgreSQL service built on modern cloud infrastructure.

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

<PlatformAvailability current="postgres" vitess="/vitess/architecture" />

## Overview

Our architecture provides high availability through availability zone distribution, automated failover, and redundancy across multiple components. PlanetScale Postgres is built around a `shared nothing` architecture in alignment with PlanetScale's [principles of extreme fault tolerance](https://planetscale.com/blog/the-principles-of-extreme-fault-tolerance).

This document explains the core components of our PostgreSQL infrastructure, from regional deployment patterns to cluster configuration options and operational capabilities.

## Regional and availability zone architecture

### Geographic distribution

PlanetScale Postgres deploys database clusters across multiple availability zones within a single region. This design provides:

* **Zone-level fault tolerance**: Database instances are automatically distributed across separate availability zones
* **Network isolation**: Each availability zone operates independently with its own network infrastructure

### Cluster design

<Frame>
  <img src="https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/postgres-arch-diagram.png?fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=c20f53698428814045e9bc242956ffd9" alt="PlanetScale Postgres cluster topology" width="1754" height="1200" data-path="postgres/postgres-arch-diagram.png" />
</Frame>

PlanetScale Postgres uses a primary-replica architecture distributed across availability zones to provide high availability without compromising performance. This design ensures that your database can survive infrastructure failures while maintaining fast read and write operations.

### Primary-replica architecture

Each production PostgreSQL cluster consists of:

**Primary instance (1)**:

* Handles all write operations
* Located in one availability zone
* Serves read operations when replicas are unavailable
* Source of truth for data replication

**Replica instances (2)**:

* Handle read-only operations
* Located in separate availability zones from primary
* Continuously synchronized with primary through streaming replication
* Available for promotion to primary during failover events

## Orchestration layer

The orchestration layer manages the lifecycle, health, and operations of PostgreSQL clusters across multiple availability zones. This infrastructure layer handles everything from initial deployment to ongoing maintenance and failover operations.

### Kubernetes-based management

Our PostgreSQL clusters run on Kubernetes infrastructure, providing:

* **Automated deployment**: Consistent cluster provisioning across availability zones
* **Health monitoring**: Continuous monitoring of database instance health
* **Resource management**: Dynamic allocation of compute and storage resources
* **Configuration management**: Centralized management of PostgreSQL parameters and settings

### Custom operator

Our custom [Kubernetes operator](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/) manages all cluster nodes and handles PostgreSQL operations that would otherwise require deep database knowledge. Based on our extensive experience running databases at massive scale, our operator was built for rock-solid handling of PostgreSQL replication, backup requirements, and the specific steps needed for safe failover operations:

* **Failover coordination**: Automated promotion of replica instances when primary fails
* **Backup scheduling**: Coordinated backup operations across all instances
* **Configuration synchronization**: Ensures consistent settings across primary and replicas
* **Scaling operations**: Manages instance resizing and replica addition/removal

## Data replication

**Streaming replication**:
PostgreSQL's built-in streaming replication keeps replicas synchronized with the primary in near real-time. Changes are continuously streamed to replicas, ensuring data consistency across all instances:

* Continuous data streaming from primary to replicas
* Changes confirmed by at least one replica
* Automatic lag monitoring and alerting

**Connection routing**:

* Port 5432: Direct PostgreSQL connections
* Port 6432: [PgBouncer](connecting/pgbouncer.md) connection pooling for optimized connection management
* SSL/TLS encryption required for all connections

## Instance configurability

PlanetScale Postgres offers flexible instance configuration to match your workload requirements and cost constraints. You can choose different CPU architectures, storage types, and performance characteristics to optimize for your specific use case.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/postgres-create-config.png?fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=94073bce12d09880d08626279e653d03" alt="Creating a database" width="3240" height="1914" data-path="postgres/postgres-create-config.png" />
</Frame>

### CPU architecture options

**ARM64 (AWS Graviton)**:

* Cost-optimized instances with good performance characteristics
* Compatible with all PostgreSQL features and extensions
* Lower power consumption and cost per compute unit

**x86-64 (Intel/AMD)**:

* Traditional architecture with broad compatibility
* Optimized for single-threaded performance requirements
* Full compatibility with existing tooling and applications

Learn more about [CPU architecture selection](cluster-configuration/cpu-architectures.md).

### Storage types

Storage choice significantly impacts database performance and cost. PlanetScale offers two distinct storage options optimized for different workload patterns:

**PlanetScale Metal**:

* Direct-attached NVMe storage for maximum performance
* Lower I/O latency compared to network-attached storage
* Fixed storage capacity based on instance size
* Optimal for I/O-intensive workloads

**Network-attached storage (EBS)**:

* Flexible storage scaling up to 64 TiB
* Configurable IOPS and throughput settings
* Automatic storage scaling based on usage patterns
* Cost-effective for variable storage requirements

Learn more about [storage configuration](cluster-configuration/cluster-storage.md).

### Performance and scaling relationships

<Frame>
  <img src="https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/postgres-dashboard-summary-metal.png?fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=4e48e13d3af6eb8460d9617af8131113" alt="Database dashboard summary" width="1478" height="1356" data-path="postgres/postgres-dashboard-summary-metal.png" />
</Frame>

Understanding how different configuration choices affect performance helps you optimize your database for both cost and performance. These relationships guide capacity planning and scaling decisions:

**Compute scaling**:

* Instance size directly affects CPU, memory, and network capacity
* Larger instances support more concurrent connections
* Memory allocation affects query performance and caching efficiency

**Storage performance**:

* Network-attached storage can autoscale to meet storage needs
* Network-attached storage allows for configurable IOPS and disk bandwidth
* PlanetScale Metal provides increased IOPS and ultra-low latency storage
* Throughput limits vary by instance size and storage configuration

## Operational capabilities

PlanetScale Postgres provides comprehensive operational tools that give you visibility into database performance, health, and behavior. These capabilities help you monitor, troubleshoot, and optimize your database without requiring additional setup or external tools.

### Insights and query analysis

<Frame>
  <img src="https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/postgres-insights.png?fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=9ecdae873efd3bca8329b8f3494c54d8" alt="PlanetScale Insights" width="2886" height="1818" data-path="postgres/postgres-insights.png" />
</Frame>

**Query performance insights**:

* Automatic detection of slow-running queries and anomalies
* Query execution plan analysis and recommendations
* Historical query performance tracking
* Identification of resource-intensive operations

### Metrics and monitoring

Comprehensive metrics collection provides real-time visibility into database health and performance trends. All metrics are collected automatically and made available through both the dashboard and programmatic interfaces:

**Built-in metrics collection**:

* CPU, memory, and storage utilization across all instances
* Connection count and pooling efficiency
* Replication lag and synchronization status

Learn more about [monitoring and metrics](monitoring/metrics.md).

### Logs and diagnostics

Centralized logging aggregates all database-related logs in a searchable format with 7-day retention. This unified logging system helps with troubleshooting, security monitoring, and performance analysis:

**Centralized logging**:

* PostgreSQL server logs from all instances
* Query execution logs with configurable detail levels
* Error logs with automatic categorization and alerting
* Connection and authentication logs for security monitoring

**Log analysis tools**:

* Search and filtering capabilities across all log sources
* Export capabilities for external analysis tools

### Cluster configuration options

<Frame>
  <img src="https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/postgres-config-changes.png?fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=ad1554779e5fba5ddd2144d44bad095d" alt="Tracking changes" width="2918" height="2174" data-path="postgres/postgres-config-changes.png" />
</Frame>

Flexible configuration options allow you to customize PostgreSQL behavior, enable additional functionality, and optimize performance for your specific workload requirements:

**Extension management**:

* Curated set of PostgreSQL extensions tested for compatibility
* Both Native and Community extensions available
* Managed through dashboard or CLI

Learn more about [extension configuration](extensions.md).

**Parameter tuning**:

* Pre-configured parameter sets optimized for different workload types
* Customizable PostgreSQL configuration parameters
* Automatic parameter adjustment based on instance size changes
* Configuration change tracking

Learn more about [parameter configuration](cluster-configuration/parameters.md).

**Custom Backups and Point-in-time recovery**:

<Frame>
  <img src="https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/postgres-pitr.png?fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=ab0d860a3f430875b0544458371dc534" alt="Postgres PITR" width="1766" height="1202" data-path="postgres/postgres-pitr.png" />
</Frame>

* Automated backup scheduling with configurable retention periods
* Custom backup timing to minimize impact on production workloads
* Point-in-time recovery capabilities within retention windows

Learn more about [backup and restore operations](backups.md).

## Development workflow integration

PlanetScale Postgres integrates with development workflows through database branching and comprehensive migration support. These features enable safe schema changes and smooth transitions from other PostgreSQL platforms.

### Database branching

**Environment isolation**:

* Create isolated database environments from production backups
* Independent schema and data changes without affecting production
* Cost-optimized single-instance architecture for development branches
* Automatic promotion to full high-availability architecture when needed

### Third-party integrations

PlanetScale Postgres integrates with popular monitoring and development tools through standard protocols and APIs. These integrations allow you to incorporate database metrics into existing workflows and toolchains:

**Monitoring system integrations**:

* [Prometheus endpoints](monitoring/prometheus-postgres.md) for custom metrics collection
* [Datadog integration](monitoring/prometheus-metrics-datadog-postgres.md) for unified monitoring dashboards

**Development tool integrations**:

* Standard PostgreSQL connection protocols for universal tool compatibility
* Support for popular database administration tools
* API access for programmatic management and monitoring

## Related documentation

<CardGroup>
  <Card href="/docs/postgres/cluster-configuration" title="Cluster Configuration" icon="angles-right" horizontal />

  <Card href="/docs/postgres/scaling/replicas" title="Scaling with Replicas" icon="angles-right" horizontal />

  <Card href="/docs/postgres/backups" title="Backup and Recovery" icon="angles-right" horizontal />

  <Card href="/docs/postgres/monitoring/metrics" title="Monitoring and Metrics" icon="angles-right" horizontal />

  <Card href="/docs/postgres/branching" title="Database Branching" icon="angles-right" horizontal />
</CardGroup>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
