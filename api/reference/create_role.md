---
url: https://planetscale.com/docs/api/reference/create_role
title: "Create_role"
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

This endpoint creates **Postgres role credentials**. Roles are the credential system for PlanetScale **Postgres** databases. If your database is **Vitess/MySQL**, create a [branch password](create_password.md) instead — Vitess/MySQL databases do not have roles.

The `password` returned in the response is shown **only once** and cannot be retrieved later. Record it securely as soon as the role is created. If it is lost, rotate it with [Reset role credentials](reset_role.md).

**Cleanup:** delete a role with [Delete a role](delete_role.md). If the role owns objects, has extra permissions, or created other roles, you must designate a successor — see [Managing roles](../../postgres/connecting/roles.md#deleting-a-role).

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

Branch name from `list_branches`. Example: `main`.

#### Body

application/jsonname

string

The name of the rolettl

integer

Time to live in secondsinherited\_roles

enum<string>\[\]

Roles to inherit from

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

Whether the role should have the REPLICATION attributerequire\_where\_on\_delete

string

Require WHERE clause on DELETE statementsrequire\_where\_on\_update

string

Require WHERE clause on UPDATE statements

#### Response

Returns the new credentialsid

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
