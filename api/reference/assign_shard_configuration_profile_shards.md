---
url: https://planetscale.com/docs/api/reference/assign_shard_configuration_profile_shards
title: "Assign_shard_configuration_profile_shards"
description: ""
access_date: 2026-09-17T07:50:53.049Z
current_date: 2026-09-17T07:50:53.049Z
---

Assign shards to a Neki shard configuration profile

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

configuration-profiles

/

{configuration\_profile}

/

shards

Assign shards to a Neki shard configuration profile

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

Name from `list_shard_configuration_profiles`.

#### Body

application/jsonshard\_ids

string\[\]

required

The IDs of the shards to assign

#### Response

Returns an assignment result for each requested shardid

string

required

The public ID of the shardstatus

string

required

One of assigned, unchanged, or failed

The assignment error
