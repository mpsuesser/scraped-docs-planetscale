---
url: https://planetscale.com/docs/api/reference/update_password
title: "Update_password"
description: ""
access_date: 2026-08-18T20:17:33.766Z
current_date: 2026-08-18T20:17:33.766Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Update a password

> 
### Authorization
A service token or OAuth token must have at least one of the following access or scopes in order to use this API endpoint:

**Service Token Accesses**
 `connect_production_branch`, `connect_production_read_only_branch`, `connect_branch`

**OAuth Scopes**

 | Resource | Scopes |
| :------- | :---------- |
| Organization | `manage_passwords`, `manage_production_branch_passwords`, `manage_read_only_passwords`, `manage_production_read_only_passwords` |
| Database | `manage_passwords`, `manage_production_branch_passwords`, `manage_read_only_passwords`, `manage_production_read_only_passwords` |
| Branch | `manage_passwords`, `manage_read_only_passwords` |

**Platform availability:** Vitess and [Postgres](update_role.md)


## OpenAPI

````yaml patch /organizations/{organization}/databases/{database}/branches/{branch}/passwords/{id}
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
  /organizations/{organization}/databases/{database}/branches/{branch}/passwords/{id}:
    patch:
      tags:
        - Database branch passwords
      summary: Update a password
      description: >-

        ### Authorization

        A service token or OAuth token must have at least one of the following
        access or scopes in order to use this API endpoint:


        **Service Token Accesses**
         `connect_production_branch`, `connect_production_read_only_branch`, `connect_branch`

        **OAuth Scopes**

         | Resource | Scopes |
        | :------- | :---------- |

        | Organization | `manage_passwords`,
        `manage_production_branch_passwords`, `manage_read_only_passwords`,
        `manage_production_read_only_passwords` |

        | Database | `manage_passwords`, `manage_production_branch_passwords`,
        `manage_read_only_passwords`, `manage_production_read_only_passwords` |

        | Branch | `manage_passwords`, `manage_read_only_passwords` |
      operationId: update_password
      parameters:
        - name: organization
          in: path
          required: true
          description: The name of the organization the password belongs to
          schema:
            type: string
        - name: database
          in: path
          required: true
          description: The name of the database the password belongs to
          schema:
            type: string
        - name: branch
          in: path
          required: true
          description: The name of the branch the password belongs to
          schema:
            type: string
        - name: id
          in: path
          required: true
          description: The ID of the password
          schema:
            type: string
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                name:
                  type: string
                  description: The name for the password
                cidrs:
                  type: array
                  items:
                    type: string
                  description: >-
                    List of IP addresses or CIDR ranges that can use this
                    password
      responses:
        '200':
          description: Returns the updated password
          headers: {}
          content:
            application/json:
              schema:
                type: object
                properties:
                  id:
                    type: string
                    description: The ID for the password
                  name:
                    type: string
                    description: The display name for the password
                  role:
                    type: string
                    enum:
                      - reader
                      - writer
                      - admin
                      - readwriter
                    description: The role for the password
                  cidrs:
                    items:
                      type: string
                    type: array
                    description: >-
                      List of IP addresses or CIDR ranges that can use this
                      password
                    nullable: true
                  created_at:
                    type: string
                    description: When the password was created
                  deleted_at:
                    type: string
                    description: When the password was deleted
                    nullable: true
                  expires_at:
                    type: string
                    description: When the password will expire
                    nullable: true
                  last_used_at:
                    type: string
                    description: When the password was last used to execute a query
                    nullable: true
                  expired:
                    type: boolean
                    description: True if the credentials are expired
                  direct_vtgate:
                    type: boolean
                    description: >-
                      True if the credentials connect directly to a vtgate,
                      bypassing load balancers
                  direct_vtgate_addresses:
                    items:
                      type: string
                    type: array
                    description: >-
                      The list of hosts in each availability zone providing
                      direct access to a vtgate
                  ttl_seconds:
                    type: integer
                    description: >-
                      Time to live (in seconds) for the password. The password
                      will be invalid when TTL has passed
                    nullable: true
                  access_host_url:
                    type: string
                    description: The host URL for the password
                  access_host_regional_url:
                    type: string
                    description: The regional host URL
                  access_host_regional_urls:
                    items:
                      type: string
                    type: array
                    description: The read-only replica host URLs
                  actor:
                    type: object
                    properties:
                      id:
                        type: string
                        description: The ID of the actor
                      display_name:
                        type: string
                        description: The name of the actor
                      avatar_url:
                        type: string
                        description: The URL of the actor's avatar
                    required:
                      - id
                      - display_name
                      - avatar_url
                    nullable: true
                  region:
                    type: object
                    properties:
                      id:
                        type: string
                        description: The ID of the region
                      provider:
                        type: string
                        description: Provider for the region (ex. AWS)
                      enabled:
                        type: boolean
                        description: Whether or not the region is currently active
                      public_ip_addresses:
                        items:
                          type: string
                        type: array
                        description: Public IP addresses for the region
                      display_name:
                        type: string
                        description: Name of the region
                      location:
                        type: string
                        description: Location of the region
                      slug:
                        type: string
                        description: The slug of the region
                      current_default:
                        type: boolean
                        description: >-
                          True if the region is the default for new branch
                          creation
                      mysql_supported:
                        type: boolean
                        description: Whether the region supports MySQL/Vitess databases
                      postgresql_supported:
                        type: boolean
                        description: Whether the region supports PostgreSQL databases
                    required:
                      - id
                      - provider
                      - enabled
                      - public_ip_addresses
                      - display_name
                      - location
                      - slug
                      - current_default
                      - mysql_supported
                      - postgresql_supported
                  username:
                    type: string
                    description: The username for the password
                  plain_text:
                    type: string
                    description: >-
                      The plaintext password. Null except in the response from
                      the create endpoint.
                    nullable: true
                  replica:
                    type: boolean
                    description: Whether or not the password is for a read replica
                  renewable:
                    type: boolean
                    description: Whether or not the password can be renewed
                  database_branch:
                    type: object
                    properties:
                      name:
                        type: string
                        description: The name for the branch
                      id:
                        type: string
                        description: The ID for the branch
                      production:
                        type: boolean
                        description: Whether or not the branch is a production branch
                      mysql_edge_address:
                        type: string
                        description: The address of the MySQL provider for the branch
                      private_edge_connectivity:
                        type: boolean
                        description: True if private connectivity is enabled
                    required:
                      - name
                      - id
                      - production
                      - mysql_edge_address
                      - private_edge_connectivity
                required:
                  - id
                  - name
                  - role
                  - cidrs
                  - created_at
                  - deleted_at
                  - expires_at
                  - last_used_at
                  - expired
                  - direct_vtgate
                  - direct_vtgate_addresses
                  - ttl_seconds
                  - access_host_url
                  - access_host_regional_url
                  - access_host_regional_urls
                  - actor
                  - region
                  - username
                  - plain_text
                  - replica
                  - renewable
                  - database_branch
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
