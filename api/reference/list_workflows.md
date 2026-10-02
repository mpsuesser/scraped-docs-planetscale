---
url: https://planetscale.com/docs/api/reference/list_workflows
title: "List_workflows"
description: ""
access_date: 2026-10-02T19:13:10.531Z
current_date: 2026-10-02T19:13:10.531Z
---

List workflows

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

workflows

List workflows

This endpoint is deprecated. Use the MoveTables endpoints under `/organizations/{organization}/databases/{database}/branches/{branch}/move-tables/workflows`, or [`pscale branch vtctl move-tables`](../../cli/move-tables.md), instead. Workflows created with MoveTables don’t appear in this endpoint’s responses.

#### AuthorizationsAuthorization

string

header

required

The access token received from the authorization server in the OAuth 2.0 flow.

#### Path Parametersorganization

string

required

The name of the organization the workflow belongs todatabase

string

required

The name of the database the workflow belongs to

#### Query Parametersbetween

string

Filter workflows to those active during a time range (e.g. 2025-01-01T00:00:00Z..2025-01-01T23:59:59)page

integer

default:1

If provided, specifies the page offset of returned resultsper\_page

integer

default:25

If provided, specifies the number of returned results

#### Response

Returns workflowscurrent\_page

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
