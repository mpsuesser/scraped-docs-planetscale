---
url: https://planetscale.com/docs/vitess/audit-log
title: "Audit Log"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Audit log

> The organization audit log grants [Organization Administrators](../security/access-control.md#organization-administrator) access to review **actions** performed by individual members of the organization.

In addition, each audit log includes **events** detailing who performed the **action** and when it happened.

Audit log retainment period is [based on your plan](../planetscale-plans.md):

* **Base plan** — 15 days
* **Enterprise** — 15 days

<Note>
  Organization audit log access is limited to [Organization Administrators](../security/access-control.md#organization-administrator).
</Note>

## Review your organization audit log

You can review your organization audit log under [your PlanetScale **organization** settings](https://app.planetscale.com/~/settings/audit-log).

Once there, you can filter the audit log by **Actor** and/or **Action**.

Clicking and expanding individual log **event** names empowers you to investigate additional metadata that can provide broader context around what changed.

## Audited organization events

You can track the following organization **events** in your PlanetScale account:

| PlanetScale organization events      | Actions                                                                                                                                                                                                                                            | Database Engine  |
| :----------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------- |
| access\_token                        | created  deleted  token\_leaked                                                                                                                                                                                                                    | Vitess, Postgres |
| backup\_policy                       | created  deleted                                                                                                                                                                                                                                   | Vitess           |
| branch\_maintenance\_schedule        | created  deleted  started\_maintenance\_window  finished\_maintenance\_window                                                                                                                                                                      | Vitess           |
| branch\_maintenance\_window          | created  deleted                                                                                                                                                                                                                                   | Vitess           |
| database                             | created  deleted  requested\_deletion  updated\_default\_branch  updated\_migration\_table\_name  removed\_member  added\_member                                                                                                                   | Vitess, Postgres |
| database\_branch                     | created  deleted  enabled\_safe\_migrations  disabled\_safe\_migrations  enabled\_foreign\_keys  disabled\_foreign\_keys  enabled\_vectors  updated\_cluster\_size  updated\_cluster\_config  updated\_vtgates  updated\_cluster\_update\_strategy | Vitess, Postgres |
| database\_branch\_keyspace           | created  deleted  updated  requested\_deletion                                                                                                                                                                                                     | Vitess           |
| database\_branch\_password           | created  deleted  password\_leaked  updated\_ip\_restrictions                                                                                                                                                                                      | Vitess, Postgres |
| database\_branch\_read\_only\_region | created  deleted                                                                                                                                                                                                                                   | Vitess           |
| database\_deploy\_request            | created  deleted  closed                                                                                                                                                                                                                           | Vitess           |
| database\_webhook                    | created  deleted                                                                                                                                                                                                                                   | Vitess, Postgres |
| deploy\_request\_review              | approved                                                                                                                                                                                                                                           | Vitess           |
| deployment                           | unqueued                                                                                                                                                                                                                                           | Vitess           |
| external\_datasource                 | created  deleted                                                                                                                                                                                                                                   | Vitess, Postgres |
| integration                          | created  deleted                                                                                                                                                                                                                                   | Vitess, Postgres |
| organization                         | created  deleted  joined  removed\_member  left  added\_member  enabled\_sso  disabled\_sso  enabled\_sso\_directory  disabled\_sso\_directory                                                                                                     | Vitess, Postgres |
| organization\_invitation             | created  deleted                                                                                                                                                                                                                                   | Vitess, Postgres |
| organization\_membership             | created  updated\_role                                                                                                                                                                                                                             | Vitess, Postgres |
| organization\_team                   | created  deleted  added\_member  left  removed\_member                                                                                                                                                                                             | Vitess, Postgres |
| postgres\_role                       | created  deleted                                                                                                                                                                                                                                   | PostgreSQL       |
| regional\_failover\_event            | created  deleted                                                                                                                                                                                                                                   | Vitess, Postgres |
| service\_token                       | created  deleted  updated\_bulk\_database\_access  token\_leaked                                                                                                                                                                                   | Vitess, Postgres |
| user                                 | created  deleted  signed\_in                                                                                                                                                                                                                       | Vitess, Postgres |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
