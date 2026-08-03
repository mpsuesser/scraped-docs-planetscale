---
url: https://planetscale.com/docs/postgres/connecting/client-error-reference
title: "Client Error Reference"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PostgreSQL Proxy - Client Error Reference

> This document provides a comprehensive reference of all error messages that the Exosphere PostgreSQL proxy may send to clients. These errors follow the PostgreSQL wire protocol format and include standard SQLSTATE error codes.

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

## Error format

All errors sent by Exosphere follow the PostgreSQL ErrorResponse message format:

| Field    | Description                                |
| :------- | :----------------------------------------- |
| Severity | ERROR, FATAL, PANIC, WARNING, NOTICE, etc. |
| Code     | 5-character SQLSTATE code (e.g., "28P01")  |
| Message  | Human-readable error description           |
| Hint     | Optional suggestion for resolution         |

## Error severity levels

| Severity    | Description                             | Client Action                 |
| ----------- | --------------------------------------- | ----------------------------- |
| **FATAL**   | Connection-terminating error            | Must reconnect                |
| **ERROR**   | Request failed but connection remains   | Can retry or continue         |
| **WARNING** | Potential issue but operation continues | Take note, prepare for action |
| **NOTICE**  | Informational message                   | For awareness only            |

## Error categories

### Authentication and authorization errors

#### SSL/TLS required

* **Severity:** FATAL
* **Code:** 28000 (invalid\_authorization\_specification)
* **Message:** "SSL connection is required"
* **Hint:** "Use sslmode=require or connect with SSL enabled"
* **When:** Client attempts unencrypted connection when TLS is mandatory
* **Resolution:** Configure client to use SSL/TLS (e.g., `sslmode=verify-full` in connection string)

#### Invalid user format

* **Severity:** FATAL
* **Code:** 28000 (invalid\_authorization\_specification)
* **Message:** "invalid user format: username must include branch (e.g., user.branch)"
* **When:** Username doesn't follow required format for branch routing
* **Resolution:** Include branch identifier in username (format: `username.branchname`)

#### Authentication failure

* **Severity:** FATAL
* **Code:** 28P01 (invalid\_password)
* **Message:** Various authentication-specific messages
* **When:** Password validation fails
* **Resolution:** Verify credentials are correct

#### Insufficient schema privileges

* **Severity:** ERROR
* **Code:** 42501 (insufficient\_privilege)
* **Message:** "permission denied for schema public"
* **When:** A role runs DDL (such as `CREATE TABLE`) in a schema where it lacks the `CREATE` privilege. Data permissions like `pg_read_all_data`/`pg_write_all_data` do not grant DDL rights.
* **Resolution:** Grant the role `CREATE` on the schema (`GRANT CREATE ON SCHEMA public TO "<role>";`) as the default `postgres` role, or run DDL as the `postgres` role. See [Managing roles](roles.md#user-defined-roles).

### Connection and network errors

#### Startup message errors

* **Severity:** FATAL
* **Code:** 08006 (connection\_failure)
* **Messages:**
  * "failed to read startup message"
  * "startup message too short: %d bytes"
  * "incomplete startup message: expected %d bytes, got %d"
  * "failed to parse startup header"
* **When:** Initial connection handshake fails
* **Resolution:** Likely a client library bug - check for driver updates

#### Backend connection failures

* **Severity:** FATAL
* **Code:** 08006 (connection\_failure)
* **Messages:**
  * "failed to connect to upstream"
  * "failed to send startup message"
  * "failed to setup backend"
  * "connection retry timeout - branch %s unavailable after %s"
* **When:** Proxy cannot establish connection to backend database
* **Resolution:** Retry with exponential backoff - this is often transient

### Routing and branch resolution errors

#### Branch not found

* **Severity:** FATAL
* **Code:** 28000 (invalid\_authorization\_specification)
* **Message:** "branch %s does not exist"
* **When:** Specified branch identifier is not recognized
* **Resolution:** Verify branch name is correct

#### Member not found

* **Severity:** FATAL
* **Code:** 28000 (invalid\_authorization\_specification)
* **Message:** "member %s not found in branch %s"
* **When:** Specific database member requested doesn't exist
* **Resolution:** Check member name and branch configuration

#### No primary available

* **Severity:** FATAL
* **Code:** 08006 (connection\_failure)
* **Message:** "no primary available for branch %s"
* **When:** Primary database instance is unavailable
* **Resolution:** Database outage - retry with exponential backoff

#### No replica available

* **Severity:** FATAL
* **Code:** 08006 (connection\_failure)
* **Message:** "no replica available for branch %s"
* **When:** No read replicas are available for the branch
* **Resolution:** All replicas are down - retry with exponential backoff

#### No running members

* **Severity:** FATAL
* **Code:** 08006 (connection\_failure)
* **Message:** "no running members available for branch %s"
* **When:** All database instances in branch are down
* **Resolution:** Total database outage - retry, but investigate the cause as this indicates all instances are down

#### Pooler restriction

* **Severity:** FATAL
* **Code:** 28000 (invalid\_authorization\_specification)
* **Message:** "pooler only supports primary destinations for branch %s"
* **When:** Attempting to use pooler with non-primary target
* **Resolution:** Connect to replicas via port 5432 instead of 6432 (pgbouncer port doesn't support replicas)

## SQLSTATE error codes

Exosphere uses standard PostgreSQL SQLSTATE codes for compatibility:

| Code      | Class                 | Description                           | Common Scenarios                    |
| :-------- | :-------------------- | :------------------------------------ | :---------------------------------- |
| **08006** | Connection Exception  | connection\_failure                   | Network issues, backend unavailable |
| **22001** | Data Exception        | string\_data\_right\_truncation       | Value exceeds field length          |
| **23505** | Integrity Constraint  | unique\_violation                     | Duplicate key violation             |
| **28000** | Invalid Authorization | invalid\_authorization\_specification | Auth configuration issues           |
| **28P01** | Invalid Authorization | invalid\_password                     | Authentication failure              |

## Client library considerations

### Connection retry logic

* Errors with code `08006` are typically transient and safe to retry
* Errors with code `28000` or `28P01` indicate configuration issues - don't retry without changes

### Error handling best practices

1. Always check the SQLSTATE code, not just the message text
2. Implement exponential backoff for connection failures (08006)
3. Handle shutdown notices gracefully by proactively reconnecting
4. Log full error details including severity and code for debugging

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
