---
url: https://planetscale.com/docs/neki/connecting/routing-defaults
title: "Routing Defaults"
description: ""
access_date: 2026-11-16T08:05:02.601Z
current_date: 2026-11-16T08:05:02.601Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Routing defaults for roles

> Stored Neki routing settings for managed roles, roles created with CREATE ROLE, and databases

Store a `__neki.*` routing setting on a role with `ALTER ROLE ... SET`, and
every connection of that role starts with it. The application then needs no
`SET __neki.setting = value` statements or connection options.

## Settings you can store

| Setting | Values | Default |
| :- | :- | :- |
| `__neki.target` | `primary`, `replica`, `rdonly` | `primary` |
| `__neki.replica_recency` | `prefer`, `require`, `off` | `prefer` |
| `__neki.replica_locality` | `prefer`, `require`, `off` | `prefer` |
| `__neki.replica_affinity` | `none`, `session` | `none` |
| `__neki.fanout` | `single`, `multi`, `scatter` | `scatter` |
| `__neki.shard` | A shard UID, or empty | Empty |

See [Shard targeting and fanout](https://planetscale.com/docs/neki/shard-targeting-fanout.md) for what each
setting does.

## Who can store a setting

Postgres stores a `__neki.*` setting only for a role that has the `SET`
privilege on that setting. The default `postgres` role has `SET` on each
setting in the table, and it can grant `SET` to other roles.

To change the settings of a different role, Postgres also requires the `ADMIN`
option on that role. The `postgres` role has the `ADMIN` option on the roles
that it creates with `CREATE ROLE`, but not on
[managed roles](https://planetscale.com/docs/neki/connecting/roles.md), which PlanetScale creates.

## Roles created with CREATE ROLE

For example, consider a role `reporting` that only needs read access and wants
the lowest-latency reads, from replicas in the router's own cell. Connected as
`postgres`, store the settings on the role:

```sql theme={null}
CREATE ROLE reporting LOGIN PASSWORD '<PASSWORD>';
ALTER ROLE reporting SET "__neki.target" = 'replica';
ALTER ROLE reporting SET "__neki.replica_locality" = 'require';
```

New connections as `reporting` start with these settings:

```sql theme={null}
SHOW __neki.target;          -- REPLICA
SELECT pg_is_in_recovery();  -- t: the query ran on a replica
```

To remove a setting, run `ALTER ROLE reporting RESET "__neki.target";` as
`postgres`.

## Managed roles

Roles created in the dashboard, with the API, or with the PlanetScale CLI are
managed roles. The `postgres` role cannot run `ALTER ROLE` on them:

```text theme={null}
ERROR:  permission denied to alter role
DETAIL:  Only roles with the CREATEROLE attribute and the ADMIN option on role "pscale_api_<ID>" may alter this role.
```

Instead, grant the managed role `SET` on each setting, and then store the
settings while connected as that role:

**Step 1**

Connect as the managed role and get its Postgres role name:

```sql theme={null}
SELECT current_user;
```

**Step 2**

Connect as `postgres` and grant `SET` on each setting that the role will
store:

```sql theme={null}
GRANT SET ON PARAMETER "__neki.target", "__neki.replica_locality" TO <ROLE_NAME>;
```

**Step 3**

Connect as the managed role again and store the settings:

```sql theme={null}
ALTER ROLE CURRENT_USER SET "__neki.target" = 'replica';
ALTER ROLE CURRENT_USER SET "__neki.replica_locality" = 'require';
```

Without the grant, the last step fails with
`permission denied to set parameter "__neki.target"`.

A managed role with a stored `replica` or `rdonly` target starts each
connection on a replica, where Neki rejects `ALTER ROLE` with
`cannot execute ALTER ROLE: session target is not primary`. To change or remove
the setting later, switch the session to the primary first:

```sql theme={null}
SET __neki.target = 'primary';
ALTER ROLE CURRENT_USER RESET "__neki.target";
```

## Database defaults

The `postgres` role can also store a setting for every connection to a logical
database that it owns:

```sql theme={null}
ALTER DATABASE postgres SET "__neki.replica_locality" = 'require';
```

> [!WARNING]
>
> A database setting applies to every role that has no setting of its own,
> including `postgres`. With a `replica` or `rdonly` target as a database
> setting, Neki rejects writes and DDL in those sessions until they run
> `SET __neki.target = 'primary'`. Prefer role settings for `__neki.target`,
> `__neki.fanout`, and `__neki.shard`.

## Precedence

When a setting is set in more than one place, the later one in this list wins:

1. Neki's default
2. A database setting: `ALTER DATABASE ... SET`
3. A role setting: `ALTER ROLE ... SET`
4. A role setting for one database: `ALTER ROLE ... IN DATABASE ... SET`
5. The connection options, such as `PGOPTIONS='-c __neki.target=primary'`
6. `SET` in the session

An application can always override a stored setting for one connection or one
session.

## When stored settings apply

Neki reads stored settings when a connection starts. Open connections,
including those in a connection pool, keep their settings until they
reconnect. Right after you change a setting, a connection that starts on a
different router can get the earlier value for a short time.

Neki checks a stored value only when a connection starts. Test the value with
`SET` in a session before you store it. `SET` rejects an invalid value
immediately, and for `__neki.shard` it also checks that the shard exists.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
