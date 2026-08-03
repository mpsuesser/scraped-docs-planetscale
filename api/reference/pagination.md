---
url: https://planetscale.com/docs/api/reference/pagination
title: "Pagination"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Pagination

> How to paginate API responses

## Page-based pagination

The majority of our API endpoints use standard page-based pagination.

* `page`: the page number
* `per_page`: the number of items returned per page (default: 25, max: 100)

To set these values, pass them as query parameters in your request. For example, [in a list organizations `GET` request](list_organizations.md), to return the first page at 50 items per page:

<CodeGroup>
  ```curl cURL theme={null}
  curl --request GET \
       --url 'https://api.planetscale.com/v1/organizations?page=1&per_page=50' \
       --header 'Authorization: TOKEN_ID:TOKEN_SECRET' \
       --header 'accept: application/json'
  ```
</CodeGroup>

## Cursor-based pagination

For API endpoints with large result sets, we use cursor-based pagination.

Cursor endpoints will return the following in their payload.

* `cursor_start`: The ID of the first item returned.
* `cursor_end`: The ID of the last item returned.
* `has_next`: Whether there is a next page of results.
* `has_prev`: Whether there is a previous page of results.

For example:

<CodeGroup>
  ```json json theme={null}
  {
  "type": "list",
  "has_next": true,
  "has_prev": false,
  "cursor_start": "b34zxm9mkz7g",
  "cursor_end": "eeq8f2lwrlum",
  "data": []
  }
  ```
</CodeGroup>

### Cursor-based query parameters

To pagination records, the following query parameters are available:

* `starting_after`: The public\_id of the last item in the previous page.
* `ending_before`: The public\_id of the first item in the next page.
* `limit`: The number of items to return. Default DEFAULT\_LIMIT.

For example, use the following to retrieve to items after `eeq8f2lwrlum`.

<CodeGroup>
  ```curl cURL theme={null}
  curl --request GET \
       --url 'https://api.planetscale.com/v1/organizations/my-org/audit-logs?starting_after=eeq8f2lwrlum&limit=50' \
       --header 'Authorization: TOKEN_ID:TOKEN_SECRET' \
       --header 'accept: application/json'
  ```
</CodeGroup>

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
