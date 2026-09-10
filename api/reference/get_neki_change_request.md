---
url: https://planetscale.com/docs/api/reference/get_neki_change_request
title: "Get_neki_change_request"
description: ""
access_date: 2026-09-10T16:32:33.433Z
current_date: 2026-09-10T16:32:33.433Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Get a change request for a branch

> 
### Authorization
A service token or OAuth token must have at least one of the following access or scopes in order to use this API endpoint:

**Service Token Accesses**
 `read_branch`, `delete_branch`, `create_branch`, `connect_production_branch`, `connect_branch`

**OAuth Scopes**

 | Resource | Scopes |
| :------- | :---------- |
| Organization | `read_branches` |
| Database | `read_branches` |
| Branch | `read_branch` |

**Platform availability:** 


## OpenAPI

````yaml get /organizations/{organization}/databases/{database}/branches/{branch}/neki-changes/{id}
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
  - name: Billing payment method setup
    description: |2
                  Resources for adding an organization's payment method through hosted checkout.
  - name: Billing payment method
    description: |2
                  Resources for managing an organization's payment method.
  - name: Invoices
    description: |2
                  Resources for managing invoices.
  - name: Organization SSO domains
    description: |2
                  Resources for listing and verifying organization email domains used for SSO.
  - name: Organization SSO
    description: |2
                  Resources for enabling SSO, verifying email domains, and configuring an identity provider.
  - name: Team members
    description: |2
                  Resources for managing team memberships within an organization. Team members inherit access to databases assigned to their team.

                  Note: Teams managed through SSO/directory services cannot have members added or removed via API.
  - name: Organization teams
    description: |2
                  Resources for managing teams within an organization. Teams allow you to group members and grant them access to specific databases.

                  Note: Teams managed through SSO/directory services cannot be modified via API.
paths:
  /organizations/{organization}/databases/{database}/branches/{branch}/neki-changes/{id}:
    get:
      tags:
        - api-neki_changes
      summary: Get a change request for a Neki branch
      description: >-

        ### Authorization

        A service token or OAuth token must have at least one of the following
        access or scopes in order to use this API endpoint:


        **Service Token Accesses**
         `read_branch`, `delete_branch`, `create_branch`, `connect_production_branch`, `connect_branch`

        **OAuth Scopes**

         | Resource | Scopes |
        | :------- | :---------- |

        | Organization | `read_branches` |

        | Database | `read_branches` |

        | Branch | `read_branch` |
      operationId: get_neki_change_request
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
        - name: id
          in: path
          required: true
          description: The ID of the change request
          schema:
            type: string
      responses:
        '200':
          description: Returns a Neki change request
          headers: {}
          content:
            application/json:
              schema:
                type: object
                properties:
                  type:
                    type: string
                    enum:
                      - NekiAdminChangeRequest
                      - NekiClusterChangeRequest
                      - NekiConfigurationProfileChangeRequest
                      - NekiRouterChangeRequest
                      - NekiSidecarChangeRequest
                    description: The type of change request
                  id:
                    type: string
                    description: The ID of the change request
                  state:
                    type: string
                    enum:
                      - draft
                      - pending
                      - applying
                      - canceled
                      - completed
                    description: The state of the change request
                  can_delete:
                    type: boolean
                    description: Whether the change request can be deleted
                  started_at:
                    type: string
                    description: The time the change request started
                    nullable: true
                  completed_at:
                    type: string
                    description: The time the change request completed
                    nullable: true
                  created_at:
                    type: string
                    description: The time the change request was created
                  updated_at:
                    type: string
                    description: The time the change request was last updated
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
                  target_id:
                    type: string
                    description: The ID of the resource being changed
                    nullable: true
                  target_type:
                    type: string
                    enum:
                      - NekiAdmin
                      - NekiCluster
                      - NekiConfigurationProfile
                      - NekiRouter
                      - NekiSidecar
                    description: The type of resource being changed
                  target_name:
                    type: string
                    description: The name of the resource being changed
                  admin_size:
                    type: string
                    description: The name of the admin size SKU
                    nullable: true
                  admin_size_display_name:
                    type: string
                    description: The display name of the admin size SKU
                    nullable: true
                  parameters:
                    type: object
                    additionalProperties: true
                    description: The parameters requested for the target resource
                    nullable: true
                  previous_admin_size:
                    type: string
                    description: The previous name of the admin size SKU
                    nullable: true
                  previous_admin_size_display_name:
                    type: string
                    description: The previous display name of the admin size SKU
                    nullable: true
                  previous_parameters:
                    type: object
                    additionalProperties: true
                    description: The target resource parameters before the change
                    nullable: true
                  flags:
                    items:
                      type: string
                    type: array
                    description: The cluster flags
                    nullable: true
                  neki_image_version:
                    type: string
                    description: The Neki image version
                    nullable: true
                  previous_flags:
                    items:
                      type: string
                    type: array
                    description: The previous cluster flags
                    nullable: true
                  previous_neki_image_version:
                    type: string
                    description: The previous Neki image version
                    nullable: true
                  router_size:
                    type: string
                    description: The name of the router size SKU
                    nullable: true
                  router_size_display_name:
                    type: string
                    description: The display name of the router size SKU
                    nullable: true
                  replicas_per_cell:
                    type: integer
                    description: The number of replicas in each cell
                    nullable: true
                  autoscaling:
                    type: boolean
                    description: Whether the router scales horizontally within each cell
                    nullable: true
                  max_replicas_per_cell:
                    type: integer
                    description: >-
                      The maximum number of replicas in each cell when
                      autoscaling
                    nullable: true
                  target_cpu_utilization:
                    type: integer
                    description: >-
                      The target average CPU utilization percentage when
                      autoscaling, one of 40, 50, 60 or 70
                    nullable: true
                  previous_router_size:
                    type: string
                    description: The previous name of the router size SKU
                    nullable: true
                  previous_router_size_display_name:
                    type: string
                    description: The previous display name of the router size SKU
                    nullable: true
                  previous_replicas_per_cell:
                    type: integer
                    description: The previous number of replicas in each cell
                    nullable: true
                  previous_autoscaling:
                    type: boolean
                    description: The previous autoscaling state of the router
                    nullable: true
                  previous_max_replicas_per_cell:
                    type: integer
                    description: The previous maximum number of replicas in each cell
                    nullable: true
                  previous_target_cpu_utilization:
                    type: integer
                    description: The previous target average CPU utilization percentage
                    nullable: true
                  name:
                    type: string
                    description: The name of the shard configuration profile
                    nullable: true
                  previous_name:
                    type: string
                    description: The previous name of the shard configuration profile
                    nullable: true
                  cluster_size:
                    type: string
                    description: The name of the cluster size SKU
                    nullable: true
                  cluster_display_name:
                    type: string
                    description: The display name of the cluster size SKU
                    nullable: true
                  metal:
                    type: boolean
                    description: Whether the cluster size SKU uses metal instances
                    nullable: true
                  cluster_rank:
                    type: integer
                    description: The display order of the cluster size SKU
                    nullable: true
                  replicas:
                    type: integer
                    description: The number of replicas
                    nullable: true
                  shards:
                    type: integer
                    description: The number of shards
                    nullable: true
                  postgres_image_version:
                    type: string
                    description: The PostgreSQL image
                    nullable: true
                  storage:
                    type: object
                    properties:
                      minimum_storage_bytes:
                        type: integer
                        description: The minimum storage size in bytes
                        nullable: true
                      maximum_storage_bytes:
                        type: integer
                        description: The maximum storage size in bytes for autoscaling
                        nullable: true
                      storage_autoscaling:
                        type: boolean
                        description: Whether storage autoscaling is enabled
                        nullable: true
                      storage_iops:
                        type: integer
                        description: The storage IOPS
                        nullable: true
                      storage_throughput_mibs:
                        type: integer
                        description: The storage throughput in MiB/s
                        nullable: true
                    required:
                      - minimum_storage_bytes
                      - maximum_storage_bytes
                      - storage_autoscaling
                      - storage_iops
                      - storage_throughput_mibs
                    nullable: true
                  previous_cluster_size:
                    type: string
                    description: The previous name of the cluster size SKU
                    nullable: true
                  previous_cluster_display_name:
                    type: string
                    description: The previous display name of the cluster size SKU
                    nullable: true
                  previous_metal:
                    type: boolean
                    description: Whether the previous cluster size SKU used metal instances
                    nullable: true
                  previous_cluster_rank:
                    type: integer
                    description: The previous display order of the cluster size SKU
                    nullable: true
                  previous_replicas:
                    type: integer
                    description: The previous number of replicas
                    nullable: true
                  previous_shards:
                    type: integer
                    description: The previous number of shards
                    nullable: true
                  previous_postgres_image_version:
                    type: string
                    description: The previous PostgreSQL image
                    nullable: true
                  previous_storage:
                    type: object
                    properties:
                      minimum_storage_bytes:
                        type: integer
                        description: The minimum storage size in bytes
                        nullable: true
                      maximum_storage_bytes:
                        type: integer
                        description: The maximum storage size in bytes for autoscaling
                        nullable: true
                      storage_autoscaling:
                        type: boolean
                        description: Whether storage autoscaling is enabled
                        nullable: true
                      storage_iops:
                        type: integer
                        description: The storage IOPS
                        nullable: true
                      storage_throughput_mibs:
                        type: integer
                        description: The storage throughput in MiB/s
                        nullable: true
                    required:
                      - minimum_storage_bytes
                      - maximum_storage_bytes
                      - storage_autoscaling
                      - storage_iops
                      - storage_throughput_mibs
                    nullable: true
                required:
                  - type
                  - id
                  - state
                  - can_delete
                  - started_at
                  - completed_at
                  - created_at
                  - updated_at
                  - actor
                  - target_id
                  - target_type
                  - target_name
        '401':
          description: Unauthorized
        '403':
          description: Forbidden
        '404':
          description: Change request not found
          headers: {}
        '500':
          description: Internal Server Error
components:
  securitySchemes:
    oauth2:
      type: oauth2
      flows:
        authorizationCode:
          authorizationUrl: https://app.planetscale.com/oauth/authorize
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
            organization:manage_sso: Enable, configure, and disable organization SSO
            organization:promote_branches: Promote branches in an organization
            organization:read_audit_logs: Read organization audit logs
            organization:read_backups: Read backups in an organization
            organization:read_branches: Read branches in an organization
            organization:read_comments: Read deploy request comments in an organization
            organization:read_databases: Read organization databases
            organization:read_deploy_requests: Read deploy requests in an organization
            organization:read_invoices: Read organization invoices
            organization:read_members: Read members in an organization
            organization:read_organization: Read organization
            organization:read_payment_method: Read organization payment method
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
            organization:write_payment_method: Update and delete the organization payment method
            user:read_organizations: Read a user's organizations
            user:read_user: Read user
            user:write_user: Write user

````
