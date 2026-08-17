---
url: https://planetscale.com/docs/api/reference/list_keyspaces
title: "List_keyspaces"
description: ""
access_date: 2026-08-17T19:47:58.147Z
current_date: 2026-08-17T19:47:58.147Z
---

Get keyspaces

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

Get keyspaces

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

The name of the branch

#### Query Parameterspage

integer

default:1

If provided, specifies the page offset of returned resultsper\_page

integer

default:25

If provided, specifies the number of returned results

#### Response

Returns keyspacescurrent\_page

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

The previous page of results, or null when this is the first pagedata

object\[\]

required

Show child attributes
