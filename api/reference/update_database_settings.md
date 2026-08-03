---
url: https://planetscale.com/docs/api/reference/update_database_settings
title: "Update_database_settings"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Update database settings

> 
### Authorization
A service token or OAuth token must have at least one of the following access or scopes in order to use this API endpoint:

**Service Token Accesses**
 `write_database`

**OAuth Scopes**

 | Resource | Scopes |
| :------- | :---------- |
| Organization | `write_databases` |
| Database | `write_database` |

**Platform availability:** Vitess and Postgres


## OpenAPI

````yaml patch /organizations/{organization}/databases/{database}
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
  - name: Keyspace VSchemas
    description: |2
                Resources for managing VSchemas within a keyspace.
  - name: MaintenanceSchedules
    description: |2
                Resources for viewing database maintenance schedules for Vitess databases (Enterprise only).
  - name: MaintenanceWindows
    description: |2
                Resources for viewing maintenance windows for a Vitess database (Enterprise only).
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
  - name: Roles
    description: |2
                Resources for managing role credentials.
  - name: Query Insights reports
    description: |2
                Resources for downloading query insights data.
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
  /organizations/{organization}/databases/{database}:
    patch:
      tags:
        - Databases
      summary: Update database settings
      description: >-

        ### Authorization

        A service token or OAuth token must have at least one of the following
        access or scopes in order to use this API endpoint:


        **Service Token Accesses**
         `write_database`

        **OAuth Scopes**

         | Resource | Scopes |
        | :------- | :---------- |

        | Organization | `write_databases` |

        | Database | `write_database` |
      operationId: update_database_settings
      parameters:
        - name: organization
          in: path
          required: true
          description: 'Organization name slug from `list_organizations`. Example: `acme`.'
          schema:
            type: string
        - name: database
          in: path
          required: true
          description: 'Database name slug from `list_databases`. Example: `app-db`.'
          schema:
            type: string
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                new_name:
                  type: string
                  description: The name to update the database to
                automatic_migrations:
                  type: boolean
                  description: >-
                    Whether or not to copy migration data to new branches and in
                    deploy requests. (Vitess only)
                migration_framework:
                  type: string
                  description: A migration framework to use on the database. (Vitess only)
                migration_table_name:
                  type: string
                  description: >-
                    Name of table to use as migration table for the database.
                    (Vitess only)
                require_approval_for_deploy:
                  type: boolean
                  description: >-
                    Whether or not deploy requests must be approved by a
                    database administrator other than the request creator
                restrict_branch_region:
                  type: boolean
                  description: >-
                    Whether or not to limit branch creation to the same region
                    as the one selected during database creation.
                allow_data_branching:
                  type: boolean
                  description: >-
                    Whether or not data branching is allowed on the database.
                    (Vitess only)
                allow_foreign_key_constraints:
                  type: boolean
                  description: >-
                    Whether or not foreign key constraints are allowed on the
                    database. (Vitess only)
                insights_raw_queries:
                  type: boolean
                  description: >-
                    Whether or not full queries should be collected from the
                    database
                production_branch_web_console:
                  type: boolean
                  description: >-
                    Whether or not the web console can be used on the production
                    branch of the database
                default_branch:
                  type: string
                  description: The default branch of the database
      responses:
        '200':
          description: Returns the updated database
          headers: {}
          content:
            application/json:
              schema:
                type: object
                properties:
                  id:
                    type: string
                    description: The ID of the database
                  url:
                    type: string
                    description: The URL to the database API endpoint
                  branches_url:
                    type: string
                    description: The URL to retrieve this database's branches via the API
                  branches_count:
                    type: integer
                    description: The total number of database branches
                  open_schema_recommendations_count:
                    type: integer
                    description: The total number of schema recommendations
                  development_branches_count:
                    type: integer
                    description: The total number of database development branches
                  production_branches_count:
                    type: integer
                    description: The total number of database production branches
                  issues_count:
                    type: integer
                    description: The total number of ongoing issues within a database
                    nullable: true
                  multiple_admins_required_for_deletion:
                    type: boolean
                    description: If the database requires multiple admins for deletion
                  ready:
                    type: boolean
                    description: If the database is ready to be used
                  at_backup_restore_branches_limit:
                    type: boolean
                    description: >-
                      If the database has reached its backup restored branch
                      limit
                  at_development_branch_usage_limit:
                    type: boolean
                    description: If the database has reached its development branch limit
                  data_import:
                    type: object
                    properties:
                      state:
                        type: string
                        description: State of the data import
                      import_check_errors:
                        type: string
                        description: Errors encountered during the import check
                      started_at:
                        type: string
                        description: When the import started
                        nullable: true
                      finished_at:
                        type: string
                        description: When the import finished
                        nullable: true
                      data_source:
                        type: object
                        properties:
                          hostname:
                            type: string
                            description: Hostname of the data source
                          port:
                            type: integer
                            description: Port of the data source
                          database:
                            type: string
                            description: Database name of the data source
                        required:
                          - hostname
                          - port
                          - database
                    required:
                      - state
                      - import_check_errors
                      - started_at
                      - finished_at
                      - data_source
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
                  html_url:
                    type: string
                    description: The URL to see this database's branches in the web UI
                  name:
                    type: string
                    description: Name of the database
                  state:
                    type: string
                    enum:
                      - pending
                      - importing
                      - sleep_in_progress
                      - sleeping
                      - awakening
                      - import_ready
                      - ready
                    description: State of the database
                  sharded:
                    type: boolean
                    description: If the database is sharded
                  default_branch_shard_count:
                    type: integer
                    description: Number of shards in the default branch
                  default_branch_read_only_regions_count:
                    type: integer
                    description: Number of read only regions in the default branch
                  default_branch_table_count:
                    type: integer
                    description: Number of tables in the default branch schema
                  default_branch:
                    type: string
                    description: The default branch for the database
                  require_approval_for_deploy:
                    type: boolean
                    description: >-
                      Whether an approval is required to deploy schema changes
                      to this database
                  resizing:
                    type: boolean
                    description: True if a branch is currently resizing
                  resize_queued:
                    type: boolean
                    description: True if a branch has a queued resize request
                  config_changing:
                    type: boolean
                    description: True if a config change is in progress
                  config_change_queued:
                    type: boolean
                    description: True if a config change is queued for maintenance window
                  allow_data_branching:
                    type: boolean
                    description: >-
                      Whether seeding branches with data is enabled for all
                      branches
                  foreign_keys_enabled:
                    type: boolean
                    description: Whether foreign key constraints are enabled
                  automatic_migrations:
                    type: boolean
                    description: >-
                      Whether to automatically manage Rails migrations during
                      deploy requests.
                    nullable: true
                  restrict_branch_region:
                    type: boolean
                    description: Whether to restrict branch creation to one region
                  insights_raw_queries:
                    type: boolean
                    description: Whether raw SQL queries are collected
                  plan:
                    type: string
                    description: The database plan
                  insights_enabled:
                    type: boolean
                    description: True if query insights is enabled for the database
                  production_branch_web_console:
                    type: boolean
                    description: Whether web console is enabled for production branches
                  migration_table_name:
                    type: string
                    description: Table name to use for copying schema migration data.
                    nullable: true
                  migration_framework:
                    type: string
                    description: Framework used for applying migrations.
                    nullable: true
                  created_at:
                    type: string
                    description: When the database was created
                  updated_at:
                    type: string
                    description: When the database was last updated
                  schema_last_updated_at:
                    type: string
                    description: When the default branch schema was last changed.
                    nullable: true
                  kind:
                    type: string
                    enum:
                      - mysql
                      - postgresql
                    description: The kind of database
                required:
                  - id
                  - url
                  - branches_url
                  - branches_count
                  - open_schema_recommendations_count
                  - development_branches_count
                  - production_branches_count
                  - multiple_admins_required_for_deletion
                  - ready
                  - at_backup_restore_branches_limit
                  - at_development_branch_usage_limit
                  - region
                  - html_url
                  - name
                  - state
                  - sharded
                  - default_branch_shard_count
                  - default_branch_read_only_regions_count
                  - default_branch_table_count
                  - default_branch
                  - require_approval_for_deploy
                  - resizing
                  - resize_queued
                  - config_changing
                  - config_change_queued
                  - allow_data_branching
                  - foreign_keys_enabled
                  - restrict_branch_region
                  - insights_raw_queries
                  - plan
                  - insights_enabled
                  - production_branch_web_console
                  - created_at
                  - updated_at
                  - schema_last_updated_at
                  - kind
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
