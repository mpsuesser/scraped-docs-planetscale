---
url: https://planetscale.com/docs/api/reference/get_database_postgres_cidr
title: "Get_database_postgres_cidr"
description: ""
access_date: 2026-10-16T21:45:30.227Z
current_date: 2026-10-16T21:45:30.227Z
---

GET

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

#### Response

Returns an IP restriction entryid

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
