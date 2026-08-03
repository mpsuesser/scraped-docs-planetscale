---
url: https://planetscale.com/docs/api/reference/reset_role
title: "Reset_role"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

POST

/

organizations

/

{organization}

/

databases

/

{database}

/

branches

/

{branch}

/

roles

/

{id}

/

reset

This rotates the credentials of a **Postgres role** created with [Create role credentials](create_role.md) — it returns a new `password`. Vitess/MySQL branch passwords have no in-place secret rotation; [create a new password](create_password.md) instead.

The new `password` in the response is shown **only once**. Record it securely; existing connections using the old password will stop working.

#### AuthorizationsAuthorization

string

header

required

The access token received from the authorization server in the OAuth 2.0 flow.

#### Path Parametersorganization

string

required

Organization name slug from `list_organizations`. Example: `acme`.database

string

required

Database name slug from `list_databases`. Example: `app-db`.branch

string

required

Branch name from `list_branches`. Example: `main`.id

string

required

The ID of the role

#### Response

Returns the role with new passwordid

string

required

The ID of the rolename

string

required

The name of the roleaccess\_host\_url

string

required

The database connection stringprivate\_access\_host\_url

string

required

The database connection string for private connectionsprivate\_connection\_service\_name

string

required

The service name to set up private connectivityusername

string

required

The database user namebase\_username

string

required

The base username without branch routing suffixpassword

string

required

The plaintext password, available only after createdatabase\_name

string

required

The database namecreated\_at

string

required

When the role was createdupdated\_at

string

required

When the role was updateddeleted\_at

string | null

required

When the role was deletedexpires\_at

string | null

required

When the role expiresdropped\_at

string | null

required

When the role was droppeddisabled\_at

string | null

required

When the role was disableddrop\_failed

string

required

Error message available when dropping the role failsexpired

boolean

required

True if the credentials are expireddefault

boolean

required

Whether the role is the default postgres userttl

integer

required

Number of seconds before the credentials expireinherited\_roles

enum<string>\[\]

required

Database roles these credentials inherit

Available options:

`pscale_managed`,

`pg_checkpoint`,

`pg_create_subscription`,

`pg_maintain`,

`pg_monitor`,

`pg_read_all_data`,

`pg_read_all_settings`,

`pg_read_all_stats`,

`pg_signal_backend`,

`pg_stat_scan_tables`,

`pg_use_reserved_connections`,

`pg_write_all_data`,

`postgres`with\_replication

boolean

required

Whether the role has the REPLICATION attributebranch

object

required

Show child attributesactor

object

required

Show child attributesquery\_safety\_settings

object

required

Show child attributes
