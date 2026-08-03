---
url: https://planetscale.com/docs/api/reference/get_query_summary
title: "Get_query_summary"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Retrieve a summary of query statistics

> 
### Authorization
A service token or OAuth token must have at least one of the following access or scopes in order to use this API endpoint:

**Service Token Accesses**
 `read_databases`, `read_database`

**OAuth Scopes**

 | Resource | Scopes |
| :------- | :---------- |
| Organization | `read_databases` |
| Database | `read_database` |

**Platform availability:** Vitess and Postgres


## OpenAPI

````yaml get /organizations/{organization}/databases/{database}/branches/{branch}/insights/{fingerprint}/summary
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
  /organizations/{organization}/databases/{database}/branches/{branch}/insights/{fingerprint}/summary:
    get:
      tags:
        - api-query_insights
      summary: Retrieve a summary of query statistics
      description: >-

        ### Authorization

        A service token or OAuth token must have at least one of the following
        access or scopes in order to use this API endpoint:


        **Service Token Accesses**
         `read_databases`, `read_database`

        **OAuth Scopes**

         | Resource | Scopes |
        | :------- | :---------- |

        | Organization | `read_databases` |

        | Database | `read_database` |
      operationId: get_query_summary
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
        - name: branch
          in: path
          required: true
          description: 'Branch name from `list_branches`. Example: `main`.'
          schema:
            type: string
        - name: fingerprint
          in: path
          required: true
          description: The query fingerprint
          schema:
            type: string
        - name: keyspace
          in: query
          description: The keyspace to filter by
          schema:
            type: string
        - name: from
          in: query
          description: Start time for filtering query statistics (ISO 8601 timestamp)
          schema:
            type: string
        - name: to
          in: query
          description: End time for filtering query statistics (ISO 8601 timestamp)
          schema:
            type: string
        - name: period
          in: query
          description: Time period for filtering query statistics (e.g., '1h', '24h')
          schema:
            type: string
      responses:
        '200':
          description: Returns aggregated query statistics summary
          headers: {}
          content:
            application/json:
              schema:
                type: object
                properties:
                  id:
                    type: string
                    description: The ID of the query summary
                  fingerprint:
                    type: string
                    description: The query fingerprint
                  statement_type:
                    type: string
                    description: The type of SQL statement
                  keyspace:
                    type: string
                    description: The keyspace the query ran against
                  normalized_sql:
                    type: string
                    description: The normalized SQL statement
                  syntax_highlighted_sql:
                    type: string
                    description: Syntax highlighted SQL statement
                  multishard:
                    type: boolean
                    description: Whether the query is a multishard query
                  query_count:
                    type: integer
                    description: The number of times this query was executed
                  error_count:
                    type: integer
                    description: The number of times this query resulted in an error
                  tables:
                    items:
                      type: string
                    type: array
                    description: Tables accessed by the query
                  qualified_tables:
                    items:
                      type: string
                    type: array
                    description: Fully qualified tables accessed by the query
                  table_keyspaces:
                    items:
                      type: object
                      additionalProperties: true
                    type: array
                    description: Mapping of tables to their keyspaces
                  index_usages:
                    items:
                      type: object
                      additionalProperties: true
                    type: array
                    description: Index usage information
                  routing_index_usages:
                    items:
                      type: object
                      additionalProperties: true
                    type: array
                    description: Routing index usage information
                  sum_shard_queries:
                    type: integer
                    description: The total number of shard queries
                  max_shard_queries:
                    type: integer
                    description: The maximum number of shard queries for a single execution
                  avg_shard_queries:
                    type: number
                    description: The average number of shard queries
                  sum_rows_read:
                    type: integer
                    description: The total number of rows read
                  sum_rows_affected:
                    type: integer
                    description: The total number of rows affected
                  sum_rows_returned:
                    type: integer
                    description: The total number of rows returned
                  rows_read_per_returned:
                    type: number
                    description: Average rows read per row returned
                  rows_read_per_query:
                    type: number
                    description: Average rows read per query
                  rows_returned_per_query:
                    type: number
                    description: Average rows returned per query
                  rows_affected_per_query:
                    type: number
                    description: Average rows affected per query
                  sum_total_duration_millis:
                    type: integer
                    description: Total duration in milliseconds across all executions
                  sum_total_duration_percent:
                    type: number
                    description: Percentage of total query time
                  sum_cpu_duration_millis:
                    type: integer
                    description: Total CPU duration in milliseconds
                  sum_cpu_duration_percent:
                    type: number
                    description: Percentage of total CPU time
                  sum_io_duration_millis:
                    type: integer
                    description: Total IO duration in milliseconds
                  sum_io_duration_percent:
                    type: number
                    description: Percentage of total IO time
                  last_run_at:
                    type: string
                    description: When this query was last executed
                    nullable: true
                  time_per_query:
                    type: number
                    description: Average time per query execution
                  p50_latency:
                    type: number
                    description: 50th percentile latency
                  p99_latency:
                    type: number
                    description: 99th percentile latency
                  max_latency:
                    type: number
                    description: Maximum latency observed
                  egress_bytes:
                    type: integer
                    description: Total egress bytes
                  egress_bytes_per_query:
                    type: number
                    description: Average egress bytes per query
                  max_egress_bytes:
                    type: integer
                    description: Maximum egress bytes for a single execution
                  ingress_bytes:
                    type: integer
                    description: Total ingress bytes
                  ingress_bytes_per_query:
                    type: number
                    description: Average ingress bytes per query
                  max_ingress_bytes:
                    type: integer
                    description: Maximum ingress bytes for a single execution
                  blocks_read:
                    type: integer
                    description: Total blocks read from disk
                  blocks_hit:
                    type: integer
                    description: Total blocks found in cache
                  block_cache_hit_ratio:
                    type: number
                    description: Cache hit ratio for blocks
                  blocks_dirtied:
                    type: integer
                    description: Total blocks dirtied
                  blocks_written:
                    type: integer
                    description: Total blocks written
                  traffic_control_warnings:
                    type: integer
                    description: >-
                      The number of executions that triggered a traffic control
                      warning
                  traffic_control_throttled:
                    type: integer
                    description: The number of executions throttled by traffic control
                  traffic_control_checked:
                    type: integer
                    description: The number of executions checked by traffic control rules
                required:
                  - id
                  - fingerprint
                  - statement_type
                  - keyspace
                  - normalized_sql
                  - syntax_highlighted_sql
                  - multishard
                  - query_count
                  - error_count
                  - tables
                  - qualified_tables
                  - table_keyspaces
                  - index_usages
                  - routing_index_usages
                  - sum_shard_queries
                  - max_shard_queries
                  - avg_shard_queries
                  - sum_rows_read
                  - sum_rows_affected
                  - sum_rows_returned
                  - rows_read_per_returned
                  - rows_read_per_query
                  - rows_returned_per_query
                  - rows_affected_per_query
                  - sum_total_duration_millis
                  - sum_total_duration_percent
                  - sum_cpu_duration_millis
                  - sum_cpu_duration_percent
                  - sum_io_duration_millis
                  - sum_io_duration_percent
                  - last_run_at
                  - time_per_query
                  - p50_latency
                  - p99_latency
                  - max_latency
                  - egress_bytes
                  - egress_bytes_per_query
                  - max_egress_bytes
                  - ingress_bytes
                  - ingress_bytes_per_query
                  - max_ingress_bytes
                  - blocks_read
                  - blocks_hit
                  - block_cache_hit_ratio
                  - blocks_dirtied
                  - blocks_written
                  - traffic_control_warnings
                  - traffic_control_throttled
                  - traffic_control_checked
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
