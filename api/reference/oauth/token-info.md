---
url: https://planetscale.com/docs/api/reference/oauth/token-info
title: "Token Info"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Endpoint

```text
GET https://auth.planetscale.com/oauth/token/info
```

Returns information about the currently authenticated access token, including whether it’s active, scopes, and expiration.

## Authentication

This endpoint requires a valid OAuth access token in the Authorization header:

```text
Authorization: Bearer YOUR_ACCESS_TOKEN
```

## Response

### Success Response (200 OK) - Active Token

```json
{
  "active": true,
  "scope": "read_user read_databases",
  "client_id": "pscale_app_abc123",
  "token_type": "Bearer",
  "exp": 1709078400,
  "iat": 1706486400,
  "sub": "user_xyz789"
}
```boolean

Whether the token is currently active (not expired or revoked)string

Space-separated list of scopes granted to this tokenstring

The OAuth application’s client IDstring

Will always be “Bearer”integer

Unix timestamp when the token expiresinteger

Unix timestamp when the token was issued (created)string

Subject - the ID of the user who authorized the token

### Success Response (200 OK) - Inactive Token

If the token is expired, revoked, or invalid:

```json
{
  "active": false
}
```

## Example

```shellscript
curl -X GET https://auth.planetscale.com/oauth/token/info \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

## Use Cases

This endpoint is useful for:

- Validating that a token is still active
- Checking which scopes a token has access to
- Determining when a token will expire
- Identifying which user authorized the token
- Token introspection for security auditing
