---
url: https://planetscale.com/docs/api/reference/get_query_summary
title: "Get_query_summary"
description: ""
access_date: 2026-10-22T04:59:58.318Z
current_date: 2026-10-22T04:59:58.318Z
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

insights

/

{fingerprint}

/

summary

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

Branch name from `list_branches`. Example: `main`.fingerprint

string

required

The query fingerprint

#### Query Parameterskeyspace

string

required

The keyspace to filter byfrom

string

Start time for filtering query statistics (ISO 8601 timestamp)to

string

End time for filtering query statistics (ISO 8601 timestamp)period

enum<string>

Time period for filtering query statistics

Available options:

`15m`,

`1h`,

`3h`,

`6h`,

`12h`,

`1d`,

`2d`,

`7d`,

`8d`

#### Response

Returns aggregated query statistics summaryid

string

required

The ID of the query summaryfingerprint

string

required

The query fingerprintstatement\_type

string

required

The type of SQL statementkeyspace

string

required

The keyspace the query ran againstnormalized\_sql

string

required

The normalized SQL statementsyntax\_highlighted\_sql

string

required

Syntax highlighted SQL statementmultishard

boolean

required

Whether the query is a multishard queryquery\_count

integer

required

The number of times this query was executed

The number of times this query resulted in an errortables

string\[\]

required

Tables accessed by the queryqualified\_tables

string\[\]

required

Fully qualified tables accessed by the querytable\_keyspaces

string\[\]

required

Keyspaces or schemas accessed by the queryindex\_usages

object\[\]

required

Index usage informationrouting\_index\_usages

object\[\]

required

Routing index usage informationsum\_shard\_queries

integer

required

The total number of shard queriesmax\_shard\_queries

integer

required

The maximum number of shard queries for a single executionavg\_shard\_queries

number

required

The average number of shard queriesavg\_parallel\_workers

number

required

The average number of parallel workerssum\_rows\_read

integer

required

The total number of rows readsum\_rows\_affected

integer

required

The total number of rows affectedsum\_rows\_returned

integer

required

The total number of rows returnedrows\_read\_per\_returned

number

required

Average rows read per row returnedrows\_read\_per\_query

number

required

Average rows read per queryrows\_returned\_per\_query

number

required

Average rows returned per queryrows\_affected\_per\_query

number

required

Average rows affected per querysum\_total\_duration\_millis

number

required

Total duration in milliseconds across all executionssum\_total\_duration\_percent

number

required

Percentage of total query timesum\_cpu\_duration\_millis

number

required

Total CPU duration in millisecondssum\_cpu\_duration\_percent

number

required

Percentage of total CPU timesum\_io\_duration\_millis

number

required

Total IO duration in millisecondssum\_io\_duration\_percent

number

required

Percentage of total IO timelast\_run\_at

string | null

required

When this query was last executedtime\_per\_query

number

required

Average time per query executionp50\_latency

number

required

50th percentile latencyp99\_latency

number

required

99th percentile latencymax\_latency

number

required

Maximum latency observedegress\_bytes

integer

required

Total egress bytesegress\_bytes\_per\_query

number

required

Average egress bytes per querymax\_egress\_bytes

integer

required

Maximum egress bytes for a single executioningress\_bytes

integer

required

Total ingress bytesingress\_bytes\_per\_query

number

required

Average ingress bytes per querymax\_ingress\_bytes

integer

required

Maximum ingress bytes for a single executionblocks\_read

integer

required

Total blocks read from diskblocks\_hit

integer

required

Total blocks found in cacheblock\_cache\_hit\_ratio

number

required

Cache hit ratio for blocksblocks\_dirtied

integer

required

Total blocks dirtiedblocks\_written

integer

required

Total blocks writtentraffic\_control\_warnings

integer

required

The number of executions that triggered a traffic control warningtraffic\_control\_throttled

integer

required

The number of executions throttled by traffic controltraffic\_control\_checked

integer

required

The number of executions checked by traffic control rules
