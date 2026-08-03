---
url: https://planetscale.com/docs/postgres/cluster-configuration/cpu-architectures
title: "Cpu Architectures"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# CPU Architectures

> When deploying PostgreSQL databases, choosing the right CPU architecture is crucial for optimizing performance, cost, and efficiency. PlanetScale Postgres supports both x86-64 and ARM64 (aarch64) architectures, with ARM64 instances powered by AWS Graviton processors.

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

<PlatformAvailability current="postgres" />

## Architecture Overview

### x86-64 (Intel/AMD)

The x86-64 architecture has been the dominant server architecture for decades. These processors offer:

* Mature ecosystem with extensive software optimization
* Wide compatibility with existing applications and tools
* High single-threaded performance
* Established performance benchmarks and tuning practices

### ARM64 (AWS Graviton)

ARM64 represents the next generation of server processors, with [AWS Graviton chips](https://aws.amazon.com/ec2/graviton/) specifically designed for cloud workloads:

* Custom silicon optimized for cloud applications
* Superior price-performance ratio
* Lower power consumption
* Built on modern 64-bit ARM architecture

## Performance Comparison

### CPU Performance

* **Single-threaded**: x86-64 processors typically offer higher single-threaded performance
* **Multi-threaded**: ARM64 Graviton processors excel in multi-threaded workloads common in database operations
* **Memory bandwidth**: Graviton processors provide higher memory bandwidth, beneficial for data-intensive PostgreSQL operations

### PostgreSQL-Specific Performance

* **OLTP workloads**: ARM64 shows 10-20% better performance per dollar for typical transaction processing
* **Analytics workloads**: Both architectures perform similarly for complex analytical queries
* **Concurrent connections**: ARM64 handles high-concurrency scenarios more efficiently
* **Background processes**: ARM64's multi-core design benefits PostgreSQL's background maintenance tasks

## Cost Considerations

### Infrastructure Costs

* **ARM64 instances**: Typically 20-40% lower cost than equivalent x86-64 instances
* **Performance per dollar**: ARM64 generally provides better price-performance ratios
* **Energy efficiency**: ARM64 processors consume less power, reducing operational costs

### Total Cost of Ownership

* **Development overhead**: x86-64 may have lower initial setup costs due to existing tooling
* **Long-term savings**: ARM64 offers significant cost savings for sustained workloads
* **Scaling costs**: ARM64 becomes more cost-effective as your database scales

## Compatibility and Ecosystem

### Software Compatibility

* **PostgreSQL**: Full native support on both architectures ([PostgreSQL supported platforms](https://www.postgresql.org/docs/current/supported-platforms.html))
* **Extensions**: Most popular PostgreSQL extensions support ARM64 ([PostgreSQL ARM64 package repository](https://www.postgresql.org/about/news/arm64-on-aptpostgresqlorg-2033/))
* **Tools**: Database administration tools work seamlessly on both platforms
* **Drivers**: All major PostgreSQL drivers support ARM64

### Migration Considerations

* **Existing applications**: Applications using standard PostgreSQL drivers require no changes
* **Binary extensions**: Custom compiled extensions may need recompilation for ARM64
* **Performance tooling**: Some x86-specific optimization tools may not be available on ARM64

## Decision Matrix

| Consideration                   | x86-64            | ARM64 (Graviton)                           |
| :------------------------------ | :---------------- | :----------------------------------------- |
| **Price-performance ratio**     | Standard          | Superior (20-40% cost savings)             |
| **Single-threaded performance** | Higher            | Good                                       |
| **Multi-threaded performance**  | Good              | Superior                                   |
| **PostgreSQL extensions**       | Universal support | Most supported, some require recompilation |
| **Energy efficiency**           | Standard          | Superior                                   |

**Choose x86-64 when:**

* Legacy applications or custom extensions require x86-64
* Maximum single-threaded performance is critical
* Strict compliance requirements mandate x86-64
* Existing monitoring infrastructure is x86-64 specific

**Choose ARM64 (Graviton) when:**

* Cost optimization is a priority
* Running concurrent, multi-threaded workloads
* Starting new projects without legacy constraints
* Environmental impact reduction is important

## Getting Started

Both architectures are available when creating new PostgreSQL databases in PlanetScale Postgres. You can specify your preferred architecture during the database creation process. For most new deployments, ARM64 provides the best combination of performance, cost-effectiveness, and future-proofing.

Consider running performance tests with your specific workload on both architectures to make the most informed decision for your use case.

<Note>
  Once you've selected your desired CPU architecture, you can not modify a launched database cluster to be mixed CPU architecture nor modify your cluster to change CPU architecture. For help in swapping your CPU architecture, please [reach out to support](https://planetscale.com/contact?initial=support).
</Note>

## CPU Architecture availability

Depending on your target region for your deployment, there may or may not be certain cluster size configurations that are available from the underlying provider. PlanetScale aims to have the most complete availability of resources for you to use and as is such may enable or disable certain configurations over time based on availability.

<Note>
  The most accurate source of this information is on the [Create a new database](https://app.planetscale.com/new) page and then selecting the desired region.

  For customers of managed deployments, please [reach out to support](https://planetscale.com/contact?initial=support) for assistance in confirming availability for your deployment.
</Note>

## Additional Resources

<Columns cols={2}>
  <Card title="AWS Graviton Performance Studies" icon="angles-right" horizontal href="https://aws.amazon.com/ec2/graviton" />

  <Card title="PostgreSQL Performance Tuning" icon="angles-right" horizontal href="https://wiki.postgresql.org/wiki/Performance_Optimization" />

  <Card title="PostgreSQL ARM64 Support Documentation" icon="angles-right" horizontal href="https://www.postgresql.org/docs/current/supported-platforms.html" />

  <Card title="AWS RDS PostgreSQL ARM64 Migration Guide" icon="angles-right" horizontal href="https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide" />
</Columns>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
