---
url: https://planetscale.com/docs/api/reference/list_organization_members
title: "List_organization_members"
description: ""
access_date: 2026-08-18T20:17:33.766Z
current_date: 2026-08-18T20:17:33.766Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# List organization members

> 
### Authorization
A service token or OAuth token must have at least one of the following access or scopes in order to use this API endpoint:

**Service Token Accesses**
 `read_organization`

**OAuth Scopes**

 | Resource | Scopes |
| :------- | :---------- |
| Organization | `read_organization` |



## OpenAPI

````yaml get /organizations/{organization}/members
openapi: 3.0.1
info:
  title: PlanetScale API
  description: |-

    <p>PlanetScale API</p>
    &copy; 2026 PlanetScale, Inc.
  version: v1
  x-copyright: '&copy; 2026 PlanetScale, Inc.'
servers:
  - url: https://api.planetscale.com/v1
security:
  - oauth2: []
tags:
  - name: BackupPolicies
    description: |2
                Resources for managing database backup policies.
  - name: Backups
    description: |2
                Resources for managing database branch backups.
  - name: Branch changes
    description: |2
                Resources for managing cluster changes.
  - name: Branch config changes
    description: |2
                Resources for managing branch-level configuration change requests.
  - name: Cluster extensions
    description: |2
                Resources for managing cluster extension configuration.
  - name: Branch log signatures
    description: |2
                Resources for retrieving branch log access signatures.
  - name: Cluster parameters
    description: |2
                Resources for managing cluster configuration parameters.
  - name: Database branch keyspaces
    description: |2
                Resources for managing keyspaces.
  - name: Database branch passwords
    description: |2
                Resources for managing database branch passwords.
  - name: Database Postgres IP restrictions
    description: |2
                Resources for managing Postgres IP restriction entries for databases.

                Note: This endpoint is only available for PostgreSQL databases. For MySQL databases, use the Database Branch Passwords endpoint.
  - name: Databases
    description: |2
                  Resources for managing databases within an organization.
  - name: Keyspace config changes
    description: |2
                Resources for managing keyspace-level configuration change requests.
  - name: Keyspace resizes
    description: |2
                Resources for managing keyspace resize requests.
  - name: Keyspace VSchemas
    description: |2
                Resources for managing VSchemas within a keyspace.
  - name: MaintenanceSchedules
    description: |2
                Resources for viewing database maintenance schedules for Vitess databases (Enterprise only).
  - name: MaintenanceWindows
    description: |2
                Resources for viewing maintenance windows for a Vitess database (Enterprise only).
  - name: Metrics
    description: |2
                Resources for retrieving database metrics.
  - name: OAuth applications
    description: |2
                Resources for managing OAuth applications.
  - name: OAuth tokens
    description: |2
                Resources for managing OAuth tokens.
  - name: Organization members
    description: |2
                Resources for managing organization members and their roles.
  - name: Organizations
    description: |2
                  Resources for managing organizations.
  - name: Bouncer resizes
    description: |2
                Resources for managing Postgres bouncer resize requests.
  - name: Bouncers
    description: |2
                Resources for managing postgres bouncers.
  - name: Switchovers
    description: |2
                Resources for moving the primary of a Postgres branch.
  - name: Query Insights reports
    description: |2
                Resources for downloading query insights data.
  - name: Read-only replicas
    description: |2
                Resources for managing Postgres read-only replicas.
  - name: Roles
    description: |2
                Resources for managing role credentials.
  - name: Schema recommendations
    description: |2
                Resources for managing schema recommendations within a database.
  - name: Service tokens
    description: |2
                API endpoints for managing service tokens within an organization.
  - name: Shard config changes
    description: |2
                Resources for managing shard-level configuration change requests.
                Only available for custom-sharded keyspaces.
  - name: Traffic budgets
    description: |2
                Resources for managing traffic budgets.
  - name: Traffic rules
    description: |2
                Resources for managing traffic rules for a traffic budget.
  - name: Users
    description: |2
                Resources for managing users.
  - name: Workflows
    description: |2
                API endpoints for managing workflows.
  - name: Deploy requests
    description: |2
                  Resources for managing deploy requests.
  - name: Webhooks
    description: |2
                  Resources for managing database webhooks.
  - name: AuthAttemptExports
    description: |2
                  Resources for creating and downloading organization auth attempt exports.
  - name: Invoices
    description: |2
                  Resources for managing invoices.
  - name: Team members
    description: |2
                  Resources for managing team memberships within an organization. Team members inherit access to databases assigned to their team.

                  Note: Teams managed through SSO/directory services cannot have members added or removed via API.
  - name: Organization teams
    description: |2
                  Resources for managing teams within an organization. Teams allow you to group members and grant them access to specific databases.

                  Note: Teams managed through SSO/directory services cannot be modified via API.
paths:
  /organizations/{organization}/members:
    get:
      tags:
        - Organization members
      summary: List organization members
      description: >-

        ### Authorization

        A service token or OAuth token must have at least one of the following
        access or scopes in order to use this API endpoint:


        **Service Token Accesses**
         `read_organization`

        **OAuth Scopes**

         | Resource | Scopes |
        | :------- | :---------- |

        | Organization | `read_organization` |
      operationId: list_organization_members
      parameters:
        - name: organization
          in: path
          required: true
          description: The name of the organization
          schema:
            type: string
        - name: q
          in: query
          description: Search term to filter members by name or email
          schema:
            type: string
        - name: page
          in: query
          description: If provided, specifies the page offset of returned results
          schema:
            type: integer
            default: 1
        - name: per_page
          in: query
          description: If provided, specifies the number of returned results
          schema:
            type: integer
            default: 25
      responses:
        '200':
          description: Returns members of the organization
          headers: {}
          content:
            application/json:
              schema:
                type: object
                properties:
                  type:
                    type: string
                    description: The response type. Always "list" for paginated responses.
                  current_page:
                    type: integer
                    description: The current page number
                  per_page:
                    type: integer
                    description: The maximum number of results per page
                  next_page:
                    type: integer
                    description: The next page number, or null when this is the last page
                    nullable: true
                  next_page_url:
                    type: string
                    description: >-
                      The next page of results, or null when this is the last
                      page
                    nullable: true
                  prev_page:
                    type: integer
                    description: >-
                      The previous page number, or null when this is the first
                      page
                    nullable: true
                  prev_page_url:
                    type: string
                    description: >-
                      The previous page of results, or null when this is the
                      first page
                    nullable: true
                  total_count:
                    type: integer
                    description: The total number of matching results
                  total_pages:
                    type: integer
                    description: The total number of pages of matching results
                  data:
                    type: array
                    items:
                      type: object
                      properties:
                        id:
                          type: string
                          description: The ID of the membership
                        user:
                          type: object
                          properties:
                            id:
                              type: string
                              description: The ID of the user
                            display_name:
                              type: string
                              description: The display name of the user
                            name:
                              type: string
                              description: The name of the user
                            email:
                              type: string
                              description: The email of the user
                            avatar_url:
                              type: string
                              description: The URL source of the user's avatar
                            created_at:
                              type: string
                              description: When the user was created
                            updated_at:
                              type: string
                              description: When the user was last updated
                            two_factor_auth_configured:
                              type: boolean
                              description: >-
                                Whether or not the user has configured two
                                factor authentication
                            default_organization:
                              type: object
                              properties:
                                id:
                                  type: string
                                  description: The ID for the resource
                                name:
                                  type: string
                                  description: The name for the resource
                                created_at:
                                  type: string
                                  description: When the resource was created
                                updated_at:
                                  type: string
                                  description: When the resource was last updated
                                deleted_at:
                                  type: string
                                  description: When the resource was deleted, if deleted
                                  nullable: true
                              required:
                                - id
                                - name
                                - created_at
                                - updated_at
                                - deleted_at
                              nullable: true
                            sso:
                              type: boolean
                              description: Whether or not the user is managed by SSO.
                              nullable: true
                            managed:
                              type: boolean
                              description: >-
                                Whether or not the user is managed by an
                                authentication provider.
                              nullable: true
                            directory_managed:
                              type: boolean
                              description: >-
                                Whether or not the user is managed by a SSO
                                directory.
                              nullable: true
                            email_verified:
                              type: boolean
                              description: Whether or not the user is verified by email.
                              nullable: true
                          required:
                            - id
                            - display_name
                            - name
                            - email
                            - avatar_url
                            - created_at
                            - updated_at
                            - two_factor_auth_configured
                        role:
                          type: string
                          enum:
                            - member
                            - admin
                          description: The role of the user in the organization
                        created_at:
                          type: string
                          description: When the membership was created
                        updated_at:
                          type: string
                          description: When the membership was last updated
                      required:
                        - id
                        - user
                        - role
                        - created_at
                        - updated_at
                required:
                  - type
                  - current_page
                  - per_page
                  - next_page
                  - next_page_url
                  - prev_page
                  - prev_page_url
                  - total_count
                  - total_pages
                  - data
        '401':
          description: Unauthorized
        '403':
          description: Forbidden
        '404':
          description: Not Found
        '500':
          description: Internal Server Error
components:
  securitySchemes:
    oauth2:
      type: oauth2
      flows:
        authorizationCode:
          authorizationUrl: https://auth.planetscale.com/oauth/authorize
          tokenUrl: https://auth.planetscale.com/oauth/token
          scopes:
            email: Read user email
            openid: OpenID Connect scope
            profile: Read user profile
            read_databases: Read organization databases
            read_user: Read user
            read_organization: Read organization
            write_databases: Write organization databases
            write_user: Write user
            write_organization: Write organization
            branch:delete_backups: Delete backups
            branch:delete_branch: Delete a database branch
            branch:manage_passwords: Read, write, and delete branch passwords
            branch:manage_read_only_passwords: Read, write, and delete read only branch passwords
            branch:read_backups: Read backups
            branch:read_branch: Read a database branch
            branch:restore_backups: Restore this branch's backups to new branches
            branch:write_backups: Create and update backups
            branch:write_branch: Write a database branch
            database:approve_deploy_requests: Approve deploy requests in a database
            database:delete_backups: Delete backups
            database:delete_branches: Delete database branches
            database:delete_database: Delete a database
            database:delete_members: Delete members
            database:delete_production_branch_backups: Delete production backups
            database:delete_production_branches: Delete a production database branch
            database:demote_branches: Demote production database branches
            database:deploy_deploy_requests: Deploy deploy requests in a database
            database:manage_passwords: Read, write, and delete database branch passwords
            database:manage_production_branch_passwords: Read, write, and delete production branch passwords
            database:manage_production_read_only_passwords: >-
              Read, write, and delete production read only branch passwords in
              an organization
            database:manage_read_only_passwords: >-
              Read, write, and delete read only branch passwords in an
              organization
            database:promote_branches: Promote database branches
            database:read_backups: Read backups
            database:read_branches: Read database branches
            database:read_comments: Read deploy request comments in a database
            database:read_database: Read database information
            database:read_deploy_requests: Read deploy requests in a database
            database:read_members: Read members
            database:restore_backups: Restore backups to new branches
            database:restore_production_branch_backups: Restore production branch backups to new branches
            database:write_backups: Create and update backups
            database:write_branches: Write database branches
            database:write_comments: Create deploy request comments in a database
            database:write_database: Write database
            database:write_deploy_requests: Create and update deploy requests in a database
            database:write_members: Write members
            organization:approve_deploy_requests: Approve deploy requests in an organization
            organization:create_databases: Create organization databases
            organization:delete_backups: Delete backups in an organization
            organization:delete_branches: Delete branches in an organization
            organization:delete_databases: Delete organization databases
            organization:delete_members: Delete members in an organization
            organization:delete_organization: Delete organization
            organization:delete_production_branch_backups: Delete production backups in an organization
            organization:delete_production_branches: Delete a production branch in an organization
            organization:deploy_deploy_requests: Deploy deploy requests in an organization
            organization:manage_passwords: Read, write, and delete branch passwords in an organization
            organization:manage_production_branch_passwords: >-
              Read, write, and delete production branch passwords in an
              organization
            organization:manage_production_read_only_passwords: >-
              Read, write, and delete production read only branch passwords in
              an organization
            organization:manage_read_only_passwords: >-
              Read, write, and delete read only branch passwords in an
              organization
            organization:promote_branches: Promote branches in an organization
            organization:read_backups: Read backups in an organization
            organization:read_branches: Read branches in an organization
            organization:read_comments: Read deploy request comments in an organization
            organization:read_databases: Read organization databases
            organization:read_deploy_requests: Read deploy requests in an organization
            organization:read_invoices: Read organization invoices
            organization:read_members: Read members in an organization
            organization:read_organization: Read organization
            organization:restore_backups: Restore backups to new branches in an organization
            organization:restore_production_branch_backups: >-
              Restore production branch backups to new branches in an
              organization
            organization:write_backups: Create and update backups in an organization
            organization:write_branches: Write branches in an organization
            organization:write_comments: Create deploy request comments in an organization
            organization:write_databases: Write organization databases
            organization:write_deploy_requests: Create and update deploy requests in an organization
            organization:write_members: Write members in an organization
            organization:write_organization: Write organization
            user:read_organizations: Read a user's organizations
            user:read_user: Read user
            user:write_user: Write user

````
