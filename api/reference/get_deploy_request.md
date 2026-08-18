---
url: https://planetscale.com/docs/api/reference/get_deploy_request
title: "Get_deploy_request"
description: ""
access_date: 2026-08-18T20:17:33.766Z
current_date: 2026-08-18T20:17:33.766Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Get a deploy request

> 
### Authorization
A service token or OAuth token must have at least one of the following access or scopes in order to use this API endpoint:

**Service Token Accesses**
 `read_deploy_request`

**OAuth Scopes**

 | Resource | Scopes |
| :------- | :---------- |
| Organization | `read_deploy_requests` |
| Database | `read_deploy_requests` |

**Platform availability:** Vitess only


## OpenAPI

````yaml get /organizations/{organization}/databases/{database}/deploy-requests/{number}
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
  /organizations/{organization}/databases/{database}/deploy-requests/{number}:
    get:
      tags:
        - Deploy requests
      summary: Get a deploy request
      description: >-

        ### Authorization

        A service token or OAuth token must have at least one of the following
        access or scopes in order to use this API endpoint:


        **Service Token Accesses**
         `read_deploy_request`

        **OAuth Scopes**

         | Resource | Scopes |
        | :------- | :---------- |

        | Organization | `read_deploy_requests` |

        | Database | `read_deploy_requests` |
      operationId: get_deploy_request
      parameters:
        - name: organization
          in: path
          required: true
          description: The name of the deploy request's organization
          schema:
            type: string
        - name: database
          in: path
          required: true
          description: The name of the deploy request's database
          schema:
            type: string
        - name: number
          in: path
          required: true
          description: The number of the deploy request
          schema:
            type: integer
      responses:
        '200':
          description: Returns information about a deploy request
          headers: {}
          content:
            application/json:
              schema:
                type: object
                properties:
                  id:
                    type: string
                    description: The ID of the deploy request
                  number:
                    type: integer
                    description: The number of the deploy request
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
                  closed_by:
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
                  branch:
                    type: string
                    description: The name of the branch the deploy request was created from
                  branch_id:
                    type: string
                    description: The ID of the branch the deploy request was created from
                  branch_deleted:
                    type: boolean
                    description: Whether or not the deploy request branch was deleted
                  branch_deleted_by:
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
                  branch_deleted_at:
                    type: string
                    description: When the deploy request branch was deleted
                    nullable: true
                  into_branch:
                    type: string
                    description: >-
                      The name of the branch the deploy request will be merged
                      into
                  into_branch_sharded:
                    type: boolean
                    description: >-
                      Whether or not the branch the deploy request will be
                      merged into is sharded
                  into_branch_shard_count:
                    type: integer
                    description: >-
                      The number of shards the branch the deploy request will be
                      merged into has
                  into_branch_keyspace_count:
                    type: integer
                    description: >-
                      The number of keyspaces the branch the deploy request will
                      be merged into has
                  approved:
                    type: boolean
                    description: Whether or not the deploy request is approved
                  state:
                    type: string
                    enum:
                      - open
                      - closed
                    description: Whether the deploy request is open or closed
                  deployment_state:
                    type: string
                    enum:
                      - pending
                      - ready
                      - no_changes
                      - queued
                      - submitting
                      - in_progress
                      - pending_cutover
                      - in_progress_vschema
                      - in_progress_cancel
                      - in_progress_cutover
                      - complete
                      - complete_cancel
                      - complete_error
                      - complete_pending_revert
                      - in_progress_revert
                      - in_progress_revert_vschema
                      - complete_revert
                      - complete_revert_error
                      - cancelled
                      - error
                    description: The deployment state of the deploy request
                  deployment:
                    type: object
                    properties:
                      id:
                        type: string
                        description: The ID of the deployment
                      auto_cutover:
                        type: boolean
                        description: >-
                          Whether or not to automatically cutover once
                          deployment is finished
                      auto_delete_branch:
                        type: boolean
                        description: >-
                          Whether or not to automatically delete the head branch
                          once deployment is finished
                      created_at:
                        type: string
                        description: When the deployment was created
                      cutover_at:
                        type: string
                        description: When the cutover for the deployment was initiated
                        nullable: true
                      cutover_expiring:
                        type: boolean
                        description: Whether or not the deployment cutover will expire soon
                      deploy_check_errors:
                        type: string
                        description: Deploy check errors for the deployment.
                        nullable: true
                      finished_at:
                        type: string
                        description: When the deployment was finished
                        nullable: true
                      force_cutover_requested_at:
                        type: string
                        description: When force cutover was triggered for the deployment
                        nullable: true
                      queued_at:
                        type: string
                        description: When the deployment was queued
                        nullable: true
                      ready_to_cutover_at:
                        type: string
                        description: When the deployment was ready for cutover
                        nullable: true
                      started_at:
                        type: string
                        description: When the deployment was started
                        nullable: true
                      state:
                        type: string
                        enum:
                          - pending
                          - ready
                          - no_changes
                          - queued
                          - submitting
                          - in_progress
                          - pending_cutover
                          - in_progress_vschema
                          - in_progress_cancel
                          - in_progress_cutover
                          - complete
                          - complete_cancel
                          - complete_error
                          - complete_pending_revert
                          - in_progress_revert
                          - in_progress_revert_vschema
                          - complete_revert
                          - complete_revert_error
                          - cancelled
                          - error
                        description: The state the deployment is in
                      submitted_at:
                        type: string
                        description: When the deployment was submitted
                        nullable: true
                      updated_at:
                        type: string
                        description: When the deployment was last updated
                      into_branch:
                        type: string
                        description: >-
                          The name of the base branch the deployment will be
                          merged into
                      deploy_request_number:
                        type: integer
                        description: >-
                          The number of the deploy request associated with this
                          deployment
                      deployable:
                        type: boolean
                        description: Whether the deployment is deployable
                      preceding_deployments:
                        items:
                          type: object
                          additionalProperties: true
                        type: array
                        description: The deployments ahead of this one in the queue
                      deploy_operations:
                        type: array
                        items:
                          type: object
                          properties:
                            id:
                              type: string
                              description: The ID for the deploy operation
                            state:
                              type: string
                              enum:
                                - pending
                                - queued
                                - in_progress
                                - complete
                                - cancelled
                                - error
                              description: The state of the deploy operation
                            keyspace_name:
                              type: string
                              description: The keyspace modified by the deploy operation
                            table_name:
                              type: string
                              description: >-
                                The name of the table modifed by the deploy
                                operation
                            operation_name:
                              type: string
                              description: The operation name of the deploy operation
                            eta_seconds:
                              type: number
                              description: >-
                                The estimated seconds until completion for the
                                deploy operation
                              nullable: true
                            progress_percentage:
                              type: number
                              description: The percent completion for the deploy operation
                              nullable: true
                            deploy_error_docs_url:
                              type: string
                              description: >-
                                A link to documentation explaining the deploy
                                error, if present
                              nullable: true
                            ddl_statement:
                              type: string
                              description: The DDL statement for the deploy operation
                            syntax_highlighted_ddl:
                              type: string
                              description: >-
                                A syntax-highlighted DDL statement for the
                                deploy operation
                            created_at:
                              type: string
                              description: When the deploy operation was created
                            updated_at:
                              type: string
                              description: When the deploy operation was last updated
                            throttled_at:
                              type: string
                              description: When the deploy operation was last throttled
                              nullable: true
                            can_drop_data:
                              type: boolean
                              description: >-
                                Whether or not the deploy operation is capable
                                of dropping data
                            table_locked:
                              type: boolean
                              description: >-
                                Whether or not the table modified by the deploy
                                operation is currently locked
                            table_recently_used:
                              type: boolean
                              description: >-
                                Whether or not the table modified by the deploy
                                operation was recently used
                            table_recently_used_at:
                              type: string
                              description: >-
                                When the table modified by the deploy operation
                                was last used
                              nullable: true
                            removed_foreign_key_names:
                              items:
                                type: string
                              type: array
                              description: Names of foreign keys removed by this operation
                              nullable: true
                            deploy_errors:
                              type: string
                              description: Deploy errors for the deploy operation
                              nullable: true
                          required:
                            - id
                            - state
                            - keyspace_name
                            - table_name
                            - operation_name
                            - eta_seconds
                            - progress_percentage
                            - deploy_error_docs_url
                            - ddl_statement
                            - syntax_highlighted_ddl
                            - created_at
                            - updated_at
                            - throttled_at
                            - can_drop_data
                            - table_locked
                            - table_recently_used
                            - table_recently_used_at
                            - removed_foreign_key_names
                            - deploy_errors
                      deploy_operation_summaries:
                        type: array
                        items:
                          type: object
                          properties:
                            id:
                              type: string
                              description: The ID for the deploy operation summary
                            created_at:
                              type: string
                              description: When the deploy operation summary was created
                            deploy_errors:
                              type: string
                              description: Deploy errors for the deploy operation summary
                            ddl_statement:
                              type: string
                              description: >-
                                The DDL statement for the deploy operation
                                summary
                            eta_seconds:
                              type: integer
                              description: >-
                                The estimated seconds until completion for the
                                deploy operation summary
                            keyspace_name:
                              type: string
                              description: >-
                                The keyspace modified by the deploy operation
                                summary
                            operation_name:
                              type: string
                              description: >-
                                The operation name of the deploy operation
                                summary
                            progress_percentage:
                              type: number
                              description: >-
                                The percent completion for the deploy operation
                                summary
                            state:
                              type: string
                              enum:
                                - pending
                                - in_progress
                                - complete
                                - cancelled
                                - error
                              description: The state of the deploy operation summary
                            syntax_highlighted_ddl:
                              type: string
                              description: >-
                                A syntax-highlighted DDL statement for the
                                deploy operation summary
                            table_name:
                              type: string
                              description: >-
                                The name of the table modifed by the deploy
                                operation summary
                            table_recently_used_at:
                              type: string
                              description: >-
                                When the table modified by the deploy operation
                                summary was last used
                              nullable: true
                            throttled_at:
                              type: string
                              description: >-
                                When the deploy operation summary was last
                                throttled
                              nullable: true
                            removed_foreign_key_names:
                              items:
                                type: string
                              type: array
                              description: >-
                                Names of foreign keys removed by this operation
                                summary
                            shard_count:
                              type: integer
                              description: >-
                                The number of shards in the keyspace modified by
                                the deploy operation summary
                            shard_names:
                              items:
                                type: string
                              type: array
                              description: >-
                                Names of shards in the keyspace modified by the
                                deploy operation summary
                            can_drop_data:
                              type: boolean
                              description: >-
                                Whether or not the deploy operation summary is
                                capable of dropping data
                            table_recently_used:
                              type: boolean
                              description: >-
                                Whether or not the table modified by the deploy
                                operation summary was recently used
                            sharded:
                              type: boolean
                              description: >-
                                Whether or not the keyspace modified by the
                                deploy operation summary is sharded
                            operations:
                              type: array
                              items:
                                type: object
                                properties:
                                  id:
                                    type: string
                                    description: The ID for the deploy operation
                                  shard:
                                    type: string
                                    description: >-
                                      The shard the deploy operation is being
                                      performed on
                                  state:
                                    type: string
                                    enum:
                                      - pending
                                      - queued
                                      - in_progress
                                      - complete
                                      - cancelled
                                      - error
                                    description: The state of the deploy operation
                                  progress_percentage:
                                    type: number
                                    description: >-
                                      The percent completion for the deploy
                                      operation
                                  eta_seconds:
                                    type: integer
                                    description: >-
                                      The estimated seconds until completion for
                                      the deploy operation
                                required:
                                  - id
                                  - shard
                                  - state
                                  - progress_percentage
                                  - eta_seconds
                          required:
                            - id
                            - created_at
                            - deploy_errors
                            - ddl_statement
                            - eta_seconds
                            - keyspace_name
                            - operation_name
                            - progress_percentage
                            - state
                            - syntax_highlighted_ddl
                            - table_name
                            - table_recently_used_at
                            - throttled_at
                            - removed_foreign_key_names
                            - shard_count
                            - shard_names
                            - can_drop_data
                            - table_recently_used
                            - sharded
                            - operations
                      lint_errors:
                        items:
                          type: object
                          additionalProperties: true
                        type: array
                        description: >-
                          Schema lint errors preventing the deployment from
                          completing
                      sequential_diff_dependencies:
                        items:
                          type: object
                          additionalProperties: true
                        type: array
                        description: The schema dependencies that must be satisfied
                      lookup_vindex_operations:
                        items:
                          type: object
                          additionalProperties: true
                        type: array
                        description: Lookup Vitess index operations
                      throttler_configurations:
                        type: object
                        additionalProperties: true
                        description: Deployment throttling configurations.
                        nullable: true
                      deployment_revert_request:
                        type: object
                        additionalProperties: true
                        description: >-
                          The request to revert the schema operations in this
                          deployment
                        nullable: true
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
                      cutover_actor:
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
                      cancelled_actor:
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
                      schema_last_updated_at:
                        type: string
                        description: When the schema was last updated for the deployment
                        nullable: true
                      table_locked:
                        type: boolean
                        description: Whether or not the deployment has a table locked
                      locked_table_name:
                        type: string
                        description: >-
                          The name of the table that is locked by the
                          deployment.
                        nullable: true
                      instant_ddl:
                        type: boolean
                        description: >-
                          Whether or not the deployment is an instant DDL
                          deployment
                      instant_ddl_eligible:
                        type: boolean
                        description: >-
                          Whether or not the deployment is eligible for instant
                          DDL
                      queue_paused:
                        type: boolean
                        description: >-
                          Whether the deploy queue for the target branch is
                          currently paused
                      queue_pause_reason:
                        type: string
                        description: >-
                          A human-readable reason the deploy queue is paused, if
                          known
                        nullable: true
                    required:
                      - id
                      - auto_cutover
                      - auto_delete_branch
                      - created_at
                      - cutover_at
                      - cutover_expiring
                      - finished_at
                      - force_cutover_requested_at
                      - queued_at
                      - ready_to_cutover_at
                      - started_at
                      - state
                      - submitted_at
                      - updated_at
                      - into_branch
                      - deploy_request_number
                      - deployable
                      - preceding_deployments
                      - deploy_operations
                      - deploy_operation_summaries
                      - lint_errors
                      - sequential_diff_dependencies
                      - lookup_vindex_operations
                      - deployment_revert_request
                      - schema_last_updated_at
                      - table_locked
                      - instant_ddl
                      - instant_ddl_eligible
                      - queue_paused
                      - queue_pause_reason
                  num_comments:
                    type: integer
                    description: The number of comments on the deploy request
                  html_url:
                    type: string
                    description: The PlanetScale app address for the deploy request
                  notes:
                    type: string
                    description: Notes on the deploy request
                  html_body:
                    type: string
                    description: The HTML body of the deploy request
                  created_at:
                    type: string
                    description: When the deploy request was created
                  updated_at:
                    type: string
                    description: When the deploy request was last updated
                  closed_at:
                    type: string
                    description: When the deploy request was closed
                    nullable: true
                  deployed_at:
                    type: string
                    description: When the deploy request was deployed
                    nullable: true
                required:
                  - id
                  - number
                  - actor
                  - branch
                  - branch_id
                  - branch_deleted
                  - branch_deleted_at
                  - into_branch
                  - into_branch_sharded
                  - into_branch_shard_count
                  - into_branch_keyspace_count
                  - approved
                  - state
                  - deployment_state
                  - deployment
                  - num_comments
                  - html_url
                  - notes
                  - html_body
                  - created_at
                  - updated_at
                  - closed_at
                  - deployed_at
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
