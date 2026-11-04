---
url: https://planetscale.com/docs/api/reference/get_deploy_request
title: "Get_deploy_request"
description: ""
access_date: 2026-11-04T22:40:08.968Z
current_date: 2026-11-04T22:40:08.968Z
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

deploy-requests

/

{number}

#### AuthorizationsAuthorization

string

header

required

The access token received from the authorization server in the OAuth 2.0 flow.

#### Path Parametersorganization

string

required

The name of the deploy request's organizationdatabase

string

required

The name of the deploy request's databasenumber

integer

required

The number of the deploy request

#### Response

Returns information about a deploy requestid

string

required

The ID of the deploy requestnumber

integer

required

The number of the deploy requestactor

object

required

Show child attributesbranch

string

required

The name of the branch the deploy request was created frombranch\_id

string

required

The ID of the branch the deploy request was created frombranch\_deleted

boolean

required

Whether or not the deploy request branch was deletedbranch\_deleted\_at

string | null

required

When the deploy request branch was deletedinto\_branch

string

required

The name of the branch the deploy request will be merged intointo\_branch\_sharded

boolean

required

Whether or not the branch the deploy request will be merged into is shardedinto\_branch\_shard\_count

integer

required

The number of shards the branch the deploy request will be merged into hasinto\_branch\_keyspace\_count

integer

required

The number of keyspaces the branch the deploy request will be merged into hasapproved

boolean

required

Whether or not the deploy request is approvedstate

enum<string>

required

Whether the deploy request is open or closed

Available options:

`open`,

`closed`deployment\_state

enum<string>

required

The deployment state of the deploy request

Available options:

`pending`,

`ready`,

`no_changes`,

`queued`,

`submitting`,

`in_progress`,

`pending_cutover`,

`in_progress_vschema`,

`in_progress_cancel`,

`in_progress_cutover`,

`complete`,

`complete_cancel`,

`complete_error`,

`complete_pending_revert`,

`in_progress_revert`,

`in_progress_revert_vschema`,

`complete_revert`,

`complete_revert_error`,

`cancelled`,

`error`deployment

object

required

Show child attributeshtml\_url

string

required

The PlanetScale app address for the deploy requestnotes

string

required

Notes on the deploy requesthtml\_body

string

required

The HTML body of the deploy requestcreated\_at

string

required

When the deploy request was createdupdated\_at

string

required

When the deploy request was last updatedclosed\_at

string | null

required

When the deploy request was closeddeployed\_at

string | null

required

When the deploy request was deployedclosed\_by

object | null

Show child attributesbranch\_deleted\_by

object | null

Show child attributes
