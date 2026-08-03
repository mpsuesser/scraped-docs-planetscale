---
url: https://planetscale.com/docs/api/reference/oauth/token-info
title: "Token Info"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
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

<ResponseField name="active" type="boolean">
  Whether the token is currently active (not expired or revoked)
</ResponseField>

<ResponseField name="scope" type="string">
  Space-separated list of scopes granted to this token
</ResponseField>

<ResponseField name="client_id" type="string">
  The OAuth application's client ID
</ResponseField>

<ResponseField name="token_type" type="string">
  Will always be "Bearer"
</ResponseField>

<ResponseField name="exp" type="integer">
  Unix timestamp when the token expires
</ResponseField>

<ResponseField name="iat" type="integer">
  Unix timestamp when the token was issued (created)
</ResponseField>

<ResponseField name="sub" type="string">
  Subject - the ID of the user who authorized the token
</ResponseField>

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
