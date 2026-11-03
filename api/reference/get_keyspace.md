---
url: https://planetscale.com/docs/api/reference/get_keyspace
title: "Get_keyspace"
description: ""
access_date: 2026-11-03T20:18:32.964Z
current_date: 2026-11-03T20:18:32.964Z
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

branches

/

{branch}

/

keyspaces

/

{keyspace}

#### AuthorizationsAuthorization

string

header

required

The access token received from the authorization server in the OAuth 2.0 flow.

#### Path Parametersorganization

string

required

The name of the organization the branch belongs todatabase

string

required

The name of the database the branch belongs tobranch

string

required

The name of the branchkeyspace

string

required

The name of the keyspace

#### Response

Returns information about a keyspaceid

string

required

The ID of the keyspacename

string

required

Name of the keyspaceshards

integer

required

The number of keyspace shardssharded

boolean

required

If the keyspace is shardedexternal

boolean

required

If the keyspace uses an external datasourcereplicas

integer

required

Total number of replicas in the keyspaceextra\_replicas

integer

required

Number of extra replicas in the keyspacecreated\_at

string

required

When the keyspace was createdupdated\_at

string

required

When the keyspace was last updatedcluster\_name

string

required

The SKU representing the keyspace cluster sizecluster\_display\_name

string

required

The SKU representing the keyspace cluster size for displayresizing

boolean

required

Is the keyspace currently resizingresize\_in\_progress

boolean

required

True if the keyspace has an unfinished resize requestresize\_pending

boolean

required

Is the keyspace awaiting a resizeconfig\_change\_in\_progress

boolean

required

Is the keyspace undergoing a config changeready

boolean

required

Is the keyspace provisioned and serving trafficmetal

boolean

required

Is the keyspace running on metal instancesdefault

boolean

required

Is this the default keyspace for the branchimported

boolean

required

Is this keyspace used in an importvector\_pool\_allocation

number | null

required

Percentage of buffer pool memory allocated to vector indexesnode\_ttl\_strategy

enum<string>

required

Controls when node TTL drains are allowed

Available options:

`node_ttl_follow_maintenance`,

`node_ttl_always`,

`node_ttl_off`storage

object

required

Show child attributesmax\_rollout

integer | null

required

The maximum number of shards rolled out in parallel. Null uses the infrastructure default of 1. Effective concurrency is capped at 32.replication\_durability\_constraints

object

required

Show child attributesvreplication\_flags

object

required

Show child attributesthrottler

object

required

Show child attributesmysqld\_options

object

required

MySQL daemon configuration optionsvttablet\_options

object

required

VTTablet configuration options
