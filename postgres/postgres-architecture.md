---
url: https://planetscale.com/docs/postgres/postgres-architecture
title: "Postgres Architecture"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Overview

Our architecture provides high availability through availability zone distribution, automated failover, and redundancy across multiple components. PlanetScale Postgres is built around a `shared nothing` architecture in alignment with PlanetScale’s [principles of extreme fault tolerance](https://planetscale.com/blog/the-principles-of-extreme-fault-tolerance).

This document explains the core components of our PostgreSQL infrastructure, from regional deployment patterns to cluster configuration options and operational capabilities.

## Regional and availability zone architecture

### Geographic distribution

PlanetScale Postgres deploys database clusters across multiple availability zones within a single region. This design provides:

- **Zone-level fault tolerance**: Database instances are automatically distributed across separate availability zones
- **Network isolation**: Each availability zone operates independently with its own network infrastructure

### Cluster design

![PlanetScale Postgres cluster topology](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/postgres-arch-diagram.png?w=2500&fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=75d3b8d73ba0db0cb8c3a3343fd2be9d)

PlanetScale Postgres cluster topology

PlanetScale Postgres uses a primary-replica architecture distributed across availability zones to provide high availability without compromising performance. This design ensures that your database can survive infrastructure failures while maintaining fast read and write operations.

### Primary-replica architecture

Each production PostgreSQL cluster consists of:

**Primary instance (1)**:

- Handles all write operations
- Located in one availability zone
- Serves read operations when replicas are unavailable
- Source of truth for data replication

**Replica instances (2)**:

- Handle read-only operations
- Located in separate availability zones from primary
- Continuously synchronized with primary through streaming replication
- Available for promotion to primary during failover events

## Orchestration layer

The orchestration layer manages the lifecycle, health, and operations of PostgreSQL clusters across multiple availability zones. This infrastructure layer handles everything from initial deployment to ongoing maintenance and failover operations.

### Kubernetes-based management

Our PostgreSQL clusters run on Kubernetes infrastructure, providing:

- **Automated deployment**: Consistent cluster provisioning across availability zones
- **Health monitoring**: Continuous monitoring of database instance health
- **Resource management**: Dynamic allocation of compute and storage resources
- **Configuration management**: Centralized management of PostgreSQL parameters and settings

### Custom operator

Our custom [Kubernetes operator](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/) manages all cluster nodes and handles PostgreSQL operations that would otherwise require deep database knowledge. Based on our extensive experience running databases at massive scale, our operator was built for rock-solid handling of PostgreSQL replication, backup requirements, and the specific steps needed for safe failover operations:

- **Failover coordination**: Automated promotion of replica instances when primary fails
- **Backup scheduling**: Coordinated backup operations across all instances
- **Configuration synchronization**: Ensures consistent settings across primary and replicas
- **Scaling operations**: Manages instance resizing and replica addition/removal

## Data replication

**Streaming replication**: PostgreSQL’s built-in streaming replication keeps replicas synchronized with the primary in near real-time. Changes are continuously streamed to replicas, ensuring data consistency across all instances:

- Continuous data streaming from primary to replicas
- Changes confirmed by at least one replica
- Automatic lag monitoring and alerting

**Connection routing**:

- Port 5432: Direct PostgreSQL connections
- Port 6432: [PgBouncer](connecting/pgbouncer.md) connection pooling for optimized connection management
- SSL/TLS encryption required for all connections

## Instance configurability

PlanetScale Postgres offers flexible instance configuration to match your workload requirements and cost constraints. You can choose different CPU architectures, storage types, and performance characteristics to optimize for your specific use case.

![Creating a database](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/postgres-create-config.png?w=2500&fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=b71965b73aff18ab9c69cc1c494babf2)

Creating a database

### CPU architecture options

**ARM64 (AWS Graviton)**:

- Cost-optimized instances with good performance characteristics
- Compatible with all PostgreSQL features and extensions
- Lower power consumption and cost per compute unit

**x86-64 (Intel/AMD)**:

- Traditional architecture with broad compatibility
- Optimized for single-threaded performance requirements
- Full compatibility with existing tooling and applications

Learn more about [CPU architecture selection](cluster-configuration/cpu-architectures.md).

### Storage types

Storage choice significantly impacts database performance and cost. PlanetScale offers two distinct storage options optimized for different workload patterns:

**PlanetScale Metal**:

- Direct-attached NVMe storage for maximum performance
- Lower I/O latency compared to network-attached storage
- Fixed storage capacity based on instance size
- Optimal for I/O-intensive workloads

**Network-attached storage (EBS)**:

- Flexible storage scaling up to 64 TiB
- Configurable IOPS and throughput settings
- Automatic storage scaling based on usage patterns
- Cost-effective for variable storage requirements

Learn more about [storage configuration](cluster-configuration/cluster-storage.md).

### Performance and scaling relationships

![Database dashboard summary](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/postgres-dashboard-summary-metal.png?w=2500&fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=97572840d235e12488b712072fd69551)

Database dashboard summary

Understanding how different configuration choices affect performance helps you optimize your database for both cost and performance. These relationships guide capacity planning and scaling decisions:

**Compute scaling**:

- Instance size directly affects CPU, memory, and network capacity
- Larger instances support more concurrent connections
- Memory allocation affects query performance and caching efficiency

**Storage performance**:

- Network-attached storage can autoscale to meet storage needs
- Network-attached storage allows for configurable IOPS and disk bandwidth
- PlanetScale Metal provides increased IOPS and ultra-low latency storage
- Throughput limits vary by instance size and storage configuration

## Operational capabilities

PlanetScale Postgres provides comprehensive operational tools that give you visibility into database performance, health, and behavior. These capabilities help you monitor, troubleshoot, and optimize your database without requiring additional setup or external tools.

### Insights and query analysis

![PlanetScale Insights](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/postgres-insights.png?w=2500&fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=20f6acaaa66a6eabf675c78a25d4f8c8)

PlanetScale Insights

**Query performance insights**:

- Automatic detection of slow-running queries and anomalies
- Query execution plan analysis and recommendations
- Historical query performance tracking
- Identification of resource-intensive operations

### Metrics and monitoring

Comprehensive metrics collection provides real-time visibility into database health and performance trends. All metrics are collected automatically and made available through both the dashboard and programmatic interfaces:

**Built-in metrics collection**:

- CPU, memory, and storage utilization across all instances
- Connection count and pooling efficiency
- Replication lag and synchronization status

Learn more about [monitoring and metrics](monitoring/metrics.md).

### Logs and diagnostics

Centralized logging aggregates all database-related logs in a searchable format with 7-day retention. This unified logging system helps with troubleshooting, security monitoring, and performance analysis:

**Centralized logging**:

- PostgreSQL server logs from all instances
- Query execution logs with configurable detail levels
- Error logs with automatic categorization and alerting
- Connection and authentication logs for security monitoring

**Log analysis tools**:

- Search and filtering capabilities across all log sources
- Export capabilities for external analysis tools

### Cluster configuration options

![Tracking changes](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/postgres-config-changes.png?w=2500&fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=65c5ade9a3dbed00ef05247ac814070b)

Tracking changes

Flexible configuration options allow you to customize PostgreSQL behavior, enable additional functionality, and optimize performance for your specific workload requirements:

**Extension management**:

- Curated set of PostgreSQL extensions tested for compatibility
- Both Native and Community extensions available
- Managed through dashboard or CLI

Learn more about [extension configuration](extensions.md).

**Parameter tuning**:

- Pre-configured parameter sets optimized for different workload types
- Customizable PostgreSQL configuration parameters
- Automatic parameter adjustment based on instance size changes
- Configuration change tracking

Learn more about [parameter configuration](cluster-configuration/parameters.md).

**Custom Backups and Point-in-time recovery**:

![Postgres PITR](https://mintcdn.com/planetscale-2/NAfHErQ6-kE8SaMw/postgres/postgres-pitr.png?w=2500&fit=max&auto=format&n=NAfHErQ6-kE8SaMw&q=85&s=6226cf8b85899c6282d82370375f5e20)

Postgres PITR

- Automated backup scheduling with configurable retention periods
- Custom backup timing to minimize impact on production workloads
- Point-in-time recovery capabilities within retention windows

Learn more about [backup and restore operations](backups.md).

## Development workflow integration

PlanetScale Postgres integrates with development workflows through database branching and comprehensive migration support. These features enable safe schema changes and smooth transitions from other PostgreSQL platforms.

### Database branching

**Environment isolation**:

- Create isolated database environments from production backups
- Independent schema and data changes without affecting production
- Cost-optimized single-instance architecture for development branches
- Automatic promotion to full high-availability architecture when needed

### Third-party integrations

PlanetScale Postgres integrates with popular monitoring and development tools through standard protocols and APIs. These integrations allow you to incorporate database metrics into existing workflows and toolchains:

**Monitoring system integrations**:

- [Prometheus endpoints](monitoring/prometheus-postgres.md) for custom metrics collection
- [Datadog integration](monitoring/prometheus-metrics-datadog-postgres.md) for unified monitoring dashboards

**Development tool integrations**:

- Standard PostgreSQL connection protocols for universal tool compatibility
- Support for popular database administration tools
- API access for programmatic management and monitoring

## Related documentation

## Cluster Configuration

## Scaling with Replicas

## Backup and Recovery

## Monitoring and Metrics

## Database Branching

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
