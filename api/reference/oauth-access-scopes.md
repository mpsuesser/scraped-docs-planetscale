---
url: https://planetscale.com/docs/api/reference/oauth-access-scopes
title: "Oauth Access Scopes"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# OAuth access scopes

> The full list of OAuth application access scopes.

See the [OAuth documentation](oauth.md) for more information on creating OAuth applications on PlanetScale.

## User access

| Access permissions  | Description                 |
| :------------------ | :-------------------------- |
| read\_user          | Read user                   |
| write\_user         | Write user                  |
| read\_organizations | Read a user's organizations |

## Organization access

| Access permissions                    | Description                                         |
| :------------------------------------ | :-------------------------------------------------- |
| write\_organization                   | Write organization                                  |
| read\_organization                    | Read organization                                   |
| read\_invoices                        | Read organization invoices                          |
| delete\_organization                  | Delete organization                                 |
| read\_databases                       | Read organization databases                         |
| create\_databases                     | Create organization databases                       |
| write\_databases                      | Write organization databases                        |
| delete\_databases                     | Delete organization databases                       |
| write\_members                        | Write members                                       |
| read\_members                         | Read members                                        |
| delete\_members                       | Delete members                                      |
| read\_branches                        | Read database branches                              |
| write\_branches                       | Write database branches                             |
| delete\_branches                      | Delete database branches                            |
| promote\_branches                     | Promote database branches                           |
| delete\_production\_branches          | Delete a production database branch                 |
| manage\_production\_branch\_passwords | Read, write, and delete production branch passwords |
| write\_deploy\_requests               | Create and update deploy requests in a database     |
| read\_deploy\_requests                | Read deploy requests in a database                  |
| deploy\_deploy\_requests              | Deploy deploy requests in a database                |
| approve\_deploy\_requests             | Approve deploy requests in a database               |
| write\_comments                       | Create deploy request comments in a database        |
| read\_comments                        | Read deploy request comments in a database          |
| manage\_passwords                     | Read, write, and delete branch passwords            |
| write\_backups                        | Create and update backups                           |
| read\_backups                         | Read backups                                        |
| delete\_backups                       | Delete backups                                      |
| delete\_production\_branch\_backups   | Delete production backups                           |
| restore\_backups                      | Restore this branch's backups to new branches       |
| restore\_production\_branch\_backups  | Restore production branch backups to new            |

## Database access

| Access permissions                    | Description                                         |
| :------------------------------------ | :-------------------------------------------------- |
| write\_members                        | Write members                                       |
| read\_members                         | Read members                                        |
| delete\_members                       | Delete members                                      |
| read\_branches                        | Read database branches                              |
| write\_branches                       | Write database branches                             |
| delete\_branches                      | Delete database branches                            |
| promote\_branches                     | Promote database branches                           |
| delete\_production\_branches          | Delete a production database branch                 |
| manage\_production\_branch\_passwords | Read, write, and delete production branch passwords |
| write\_deploy requests                | Create and update deploy requests in a database     |
| read\_deploy\_requests                | Read deploy requests in a database                  |
| deploy\_deploy\_requests              | Deploy deploy requests in a database                |
| approve\_deploy\_requests             | Approve deploy requests in a database               |
| write\_comments                       | Create deploy request comments in a database        |
| read\_comments                        | Read deploy request comments in a database          |
| read\_database                        | Read database information                           |
| delete\_database                      | Delete a database                                   |
| write\_database                       | Write database                                      |
| manage\_passwords                     | Read, write, and delete branch passwords            |
| write\_backups                        | Create and update backups                           |
| read\_backups                         | Read backups                                        |
| delete\_backups                       | Delete backups                                      |
| delete\_production\_branch\_backups   | Delete production backups                           |
| restore\_backups                      | Restore this branch's backups to new branches       |
| restore\_production\_branch\_backups  | Restore production branch backups to new branches   |

## Branch access

| Access permissions | Description                                   |
| :----------------- | :-------------------------------------------- |
| manage\_passwords  | Read, write, and delete branch passwords      |
| write\_branch      | Write a database branch                       |
| read\_branch       | Read a database branch                        |
| delete\_branch     | Delete a database branch                      |
| write\_backups     | Create and update backups                     |
| read\_backups      | Read backups                                  |
| delete\_backups    | Delete backups                                |
| restore\_backups   | Restore this branch's backups to new branches |

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
