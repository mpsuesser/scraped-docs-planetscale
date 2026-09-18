---
url: https://planetscale.com/docs/api/reference/update_database_postgres_cidr
title: "Update_database_postgres_cidr"
description: ""
access_date: 2026-09-18T22:47:25.124Z
current_date: 2026-09-18T22:47:25.124Z
---

PATCH

/

organizations

/

{organization}

/

databases

/

{database}

/

cidrs

/

{id}

#### AuthorizationsAuthorization

string

header

required

The access token received from the authorization server in the OAuth 2.0 flow.

#### Path Parametersorganization

string

required

The name of the organization the database belongs todatabase

string

required

The name of the databaseid

string

required

The ID of the IP restriction entry

#### Body

application/jsonschema

string

The PostgreSQL schema to restrict access to. Leave empty to allow access to all schemas.role

string

The PostgreSQL role to restrict access to. Leave empty to allow access for all roles.cidrs

string\[\]

List of IPv4 CIDR ranges (e.g., \['192.168.1.0/24', '192.168.1.1/32'\]). Only provided fields will be updated.description

string

An optional description for the IP restriction rule. Pass an empty string to clear.

#### Response

Returns the updated IP restriction entryid

string

required

The ID of the IP allowlist entryschema

string

required

The schema name to restrict access to (optional)role

string

required

The role to restrict access to (optional)cidrs

string\[\]

required

List of CIDR rangesdescription

string | null

required

An optional description for the IP restriction rulecreated\_at

string

required

When the entry was createdupdated\_at

string

required

When the entry was updateddeleted\_at

string | null

required

When the entry was deletedactor

object

required

Show child attributes
