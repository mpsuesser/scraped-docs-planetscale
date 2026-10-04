---
url: https://planetscale.com/docs/api/reference/workflow_switch_replicas
title: "Workflow_switch_replicas"
description: ""
access_date: 2026-10-04T19:56:22.595Z
current_date: 2026-10-04T19:56:22.595Z
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

workflows

/

{number}

/

switch-replicas

This endpoint is deprecated. Use the MoveTables endpoints under `/organizations/{organization}/databases/{database}/branches/{branch}/move-tables/workflows`, or [`pscale branch vtctl move-tables`](../../cli/move-tables.md), instead.

#### AuthorizationsAuthorization

string

header

required

The access token received from the authorization server in the OAuth 2.0 flow.

FlowAuthorization Code

Authorization URL

https://app.planetscale.com/oauth/authorize

Token URL

https://auth.planetscale.com/oauth/token

#### Path Parametersorganization

string

required

The name of the organization the workflow belongs todatabase

string

required

The name of the database the workflow belongs tonumber

integer

required

The sequence number of the workflow

#### Response

Returns a workflowid

string

required

The ID of the workflowname

string

required

The name of the workflownumber

integer

required

The sequence number of the workflowstate

enum<string>

required

The state of the workflow

Available options:

`pending`,

`copying`,

`running`,

`stopped`,

`verifying_data`,

`verified_data`,

`switching_replicas`,

`switched_replicas`,

`switching_primaries`,

`switched_primaries`,

`reversing_traffic`,

`reversing_traffic_for_cancel`,

`cutting_over`,

`cutover`,

`reversed_cutover`,

`completed`,

`cancelling`,

`cancelled`,

`error`created\_at

string

required

When the workflow was createdupdated\_at

string

required

When the workflow was last updatedstarted\_at

string | null

required

When the workflow was startedcompleted\_at

string | null

required

When the workflow was completedcancelled\_at

string | null

required

When the workflow was cancelledreversed\_at

string | null

required

When the workflow was reversedretried\_at

string | null

required

When the workflow was retrieddata\_copy\_completed\_at

string | null

required

When the data copy was completedcutover\_at

string | null

required

When the cutover was completedreplicas\_switched

boolean

required

Whether or not the replicas have been switchedprimaries\_switched

boolean

required

Whether or not the primaries have been switchedswitch\_replicas\_at

string | null

required

When the replicas were switchedswitch\_primaries\_at

string | null

required

When the primaries were switchedverify\_data\_at

string | null

required

When the data was verifiedworkflow\_type

enum<string>

required

The type of the workflow

Available options:

`move_tables`workflow\_subtype

string

required

The subtype of the workflowdefer\_secondary\_keys

boolean

required

Whether or not secondary keys are deferredon\_ddl

enum<string>

required

The behavior when DDL changes during the workflow

Available options:

`IGNORE`,

`STOP`,

`EXEC`,

`EXEC_IGNORE`

The errors that occurred during the workflowmay\_retry

boolean

required

Whether or not the workflow may be retriedmay\_restart

boolean

required

Whether or not the workflow may be restartedverified\_data\_stale

boolean

required

Whether or not the verified data is stalesequence\_tables\_applied

boolean

required

Whether or not sequence tables have been createdactor

object

required

Show child attributesverify\_data\_by

object

required

Show child attributesreversed\_by

object

required

Show child attributesswitch\_replicas\_by

object

required

Show child attributesswitch\_primaries\_by

object

required

Show child attributescancelled\_by

object

required

Show child attributescompleted\_by

object

required

Show child attributesretried\_by

object

required

Show child attributescutover\_by

object

required

Show child attributesreversed\_cutover\_by

object

required

Show child attributesbranch

object

required

Show child attributessource\_keyspace

object

required

Show child attributestarget\_keyspace

object

required

Show child attributesglobal\_keyspace

object

required

Show child attributes
