---
url: https://planetscale.com/docs/api/reference/list_schema_recommendations
title: "List_schema_recommendations"
description: ""
access_date: 2026-10-22T20:45:31.714Z
current_date: 2026-10-22T20:45:31.714Z
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

schema-recommendations

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

Database name slug from `list_databases`. Example: `app-db`.

#### Query Parametersstate

enum<string>

Filter by recommendation state

Available options:

`open`,

`closed`branch

string

Filter by the name of the branch the recommendation is forpage

integer

default:1

If provided, specifies the page offset of returned resultsper\_page

integer

default:25

If provided, specifies the number of returned results

#### Response

Returns schema recommendationscurrent\_page

integer

required

The current page numberper\_page

integer

required

The maximum number of results per page

The next page number, or null when this is the last page

The next page of results, or null when this is the last pageprev\_page

integer | null

required

The previous page number, or null when this is the first pageprev\_page\_url

string | null

required

The previous page of results, or null when this is the first pagetotal\_count

integer

required

The total number of matching resultstotal\_pages

integer

required

The total number of pages of matching resultsdata

object\[\]

required

Show child attributes
