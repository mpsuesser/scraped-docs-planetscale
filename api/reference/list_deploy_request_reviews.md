---
url: https://planetscale.com/docs/api/reference/list_deploy_request_reviews
title: "List_deploy_request_reviews"
description: ""
access_date: 2026-08-03T19:40:36.600Z
current_date: 2026-08-03T19:40:36.600Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# List deploy request reviews

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

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

<PlatformAvailability current="vitess" />


## OpenAPI

````yaml get /organizations/{organization}/databases/{database}/deploy-requests/{number}/reviews
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
  /organizations/{organization}/databases/{database}/deploy-requests/{number}/reviews:
    get:
      tags:
        - Deploy requests
      summary: List deploy request reviews
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
      operationId: list_deploy_request_reviews
      parameters:
        - name: organization
          in: path
          required: true
          description: The name of the organization the deploy request belongs to
          schema:
            type: string
        - name: database
          in: path
          required: true
          description: The name of the database the deploy request belongs to
          schema:
            type: string
        - name: number
          in: path
          required: true
          description: The number of the deploy request
          schema:
            type: integer
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
          description: Returns an array of deploy request reviews
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
                  data:
                    type: array
                    items:
                      type: object
                      properties:
                        id:
                          type: string
                          description: The ID of the review
                        body:
                          type: string
                          description: The text body of the review
                        html_body:
                          type: string
                          description: The HTML body of the review
                        state:
                          type: string
                          enum:
                            - commented
                            - approved
                          description: Whether the review is a comment or approval
                        created_at:
                          type: string
                          description: When the review was created
                        updated_at:
                          type: string
                          description: When the review was last updated
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
                      required:
                        - id
                        - body
                        - html_body
                        - state
                        - created_at
                        - updated_at
                        - actor
                required:
                  - type
                  - current_page
                  - next_page
                  - next_page_url
                  - prev_page
                  - prev_page_url
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
