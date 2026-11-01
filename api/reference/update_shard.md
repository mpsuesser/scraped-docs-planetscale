---
url: https://planetscale.com/docs/api/reference/update_shard
title: "Update_shard"
description: ""
access_date: 2026-11-01T14:50:11.636Z
current_date: 2026-11-01T14:50:11.636Z
---

Update a shard for a Neki branch

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

branches

/

{branch}

/

shards

/

{id}

Update a shard for a Neki branch

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

The ID of the shard

#### Response

Redirects to the configuration profile shard endpoint
