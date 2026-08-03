---
url: https://planetscale.com/docs/postgres/connecting/client-error-reference
title: "Client Error Reference"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PostgreSQL Proxy - Client Error Reference

> This document provides a comprehensive reference of all error messages that the Exosphere PostgreSQL proxy may send to clients. These errors follow the PostgreSQL wire protocol format and include standard SQLSTATE error codes.

**Platform availability:** Postgres only

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
