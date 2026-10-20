---
url: https://planetscale.com/docs/api/reference/oauth/token-info
title: "Token Info"
description: ""
access_date: 2026-10-20T18:26:07.257Z
current_date: 2026-10-20T18:26:07.257Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Get OAuth token info

> Retrieve information about the current OAuth access token

## Endpoint

```
GET https://auth.planetscale.com/oauth/token/info
```

Returns information about the currently authenticated access token, including whether it's active, scopes, and expiration.

## Authentication

This endpoint requires a valid OAuth access token in the Authorization header:

```
Authorization: Bearer YOUR_ACCESS_TOKEN
```

## Response

### Success Response (200 OK) - Active Token

```json theme={null}
{
  "active": true,
  "scope": "read_user read_databases",
  "client_id": "pscale_app_abc123",
  "token_type": "Bearer",
  "exp": 1709078400,
  "iat": 1706486400,
  "sub": "user_xyz789"
}
```

**active** `boolean`

Whether the token is currently active (not expired or revoked)

**scope** `string`

Space-separated list of scopes granted to this token

**client\_id** `string`

The OAuth application's client ID

**token\_type** `string`

Will always be "Bearer"

**exp** `integer`

Unix timestamp when the token expires

**iat** `integer`

Unix timestamp when the token was issued (created)

**sub** `string`

Subject - the ID of the user who authorized the token

### Success Response (200 OK) - Inactive Token

If the token is expired, revoked, or invalid:

```json theme={null}
{
  "active": false
}
```

## Example

```bash theme={null}
curl -X GET https://auth.planetscale.com/oauth/token/info \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

## Use Cases

This endpoint is useful for:

* Validating that a token is still active
* Checking which scopes a token has access to
* Determining when a token will expire
* Identifying which user authorized the token
* Token introspection for security auditing


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
