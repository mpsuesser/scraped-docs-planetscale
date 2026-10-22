---
url: https://planetscale.com/docs/neki/explain-reference
title: "Explain Reference"
description: ""
access_date: 2026-10-22T18:21:49.469Z
current_date: 2026-10-22T18:21:49.469Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# EXPLAIN output reference

> Complete EXPLAIN (NEKI_PLAN) and EXPLAIN (NEKI_PG_PLAN) output for one query in every format

The following query adds up the order totals after a date, as a monthly report
would. It reads the [example schema](https://planetscale.com/docs/neki/example-schema.md):

```sql theme={null}
SELECT sum(total_amount)
FROM orders
WHERE created_at > '2026-09-09';
```

The query has no predicate on the shard key `user_id`, so the plan scatters to
every shard in the group. Each shard computes a partial sum, and the router
sums the partial sums.

The following sections give the complete output of the query for `EXPLAIN
(NEKI_PLAN)` and `EXPLAIN (NEKI_PG_PLAN)`, in each of the four output formats.
[Query planning](https://planetscale.com/docs/neki/query-planning.md#explain-in-neki) explains the `EXPLAIN`
modes and their options.

## `EXPLAIN (NEKI_PLAN)`

```sql theme={null}
EXPLAIN (NEKI_PLAN, FORMAT TEXT)
SELECT sum(total_amount)
FROM orders
WHERE created_at > '2026-09-09';
```

Replace `TEXT` with `JSON`, `XML`, or `YAML` for the other formats.

**TEXT**

```text theme={null}
Aggregate [Ordered]  (cost=0.00..2002.00 rows=1 width=32)
  ->  Collapse  (cost=0.00..2002.00 rows=2 width=32)
        ->  Route [Scatter]  (cost=0.00..2002.00 rows=2 width=32)
              Query: SELECT sum(total_amount) FROM public.orders WHERE created_at > $1::timestamp without time zone
              ShardGroup: user_data
```

**JSON**

```json theme={null}
[
  {
    "Plan": {
      "Node Type": "Aggregate",
      "Variant": "Ordered",
      "Startup Cost": 0.00,
      "Total Cost": 2002.00,
      "Plan Rows": 1,
      "Plan Width": 32,
      "Aggregates": [
        "pg_catalog.sum(sum(total_amount) /*0*/) :: out_0"
      ],
      "Cost": {
        "Self": {
          "RowsProcessed": 2,
          "Total": 0
        },
        "Subtree": {
          "NetworkRows": 2,
          "RemoteCalls": 2,
          "RowsProcessed": 2,
          "Total": 2002
        }
      },
      "Plans": [
        {
          "Node Type": "Collapse",
          "Startup Cost": 0.00,
          "Total Cost": 2002.00,
          "Plan Rows": 2,
          "Plan Width": 32,
          "Cost": {
            "Subtree": {
              "NetworkRows": 2,
              "RemoteCalls": 2,
              "Total": 2002
            }
          },
          "Plans": [
            {
              "Node Type": "Route",
              "Variant": "Scatter",
              "Startup Cost": 0.00,
              "Total Cost": 2002.00,
              "Plan Rows": 2,
              "Plan Width": 32,
              "ColumnOutputMapping": "[-1]",
              "OuterParams": "[0]",
              "ParamTypes": "[text]",
              "Query": "SELECT sum(total_amount) FROM public.orders WHERE created_at \u003e $1::timestamp without time zone",
              "ShardGroupUid": "user_data",
              "Cost": {
                "Subtree": {
                  "NetworkRows": 2,
                  "RemoteCalls": 2,
                  "Total": 2002
                }
              }
            }
          ]
        }
      ]
    }
  }
]
```

**XML**

```xml theme={null}
<explain xmlns="http://www.postgresql.org/2009/explain">
  <Query>
    <Plan>
      <Node-Type>Aggregate</Node-Type>
      <Variant>Ordered</Variant>
      <Startup-Cost>0.00</Startup-Cost>
      <Total-Cost>2002.00</Total-Cost>
      <Plan-Rows>1</Plan-Rows>
      <Plan-Width>32</Plan-Width>
      <Aggregates>pg_catalog.sum(sum(total_amount) /*0*/) :: out_0 </Aggregates>
      <Cost>
        <Self>
          <RowsProcessed>2</RowsProcessed>
          <Total>0</Total>
        </Self>
        <Subtree>
          <NetworkRows>2</NetworkRows>
          <RemoteCalls>2</RemoteCalls>
          <RowsProcessed>2</RowsProcessed>
          <Total>2002</Total>
        </Subtree>
      </Cost>
      <Plans>
        <Plan>
          <Node-Type>Collapse</Node-Type>
          <Startup-Cost>0.00</Startup-Cost>
          <Total-Cost>2002.00</Total-Cost>
          <Plan-Rows>2</Plan-Rows>
          <Plan-Width>32</Plan-Width>
          <Cost>
            <Subtree>
              <NetworkRows>2</NetworkRows>
              <RemoteCalls>2</RemoteCalls>
              <Total>2002</Total>
            </Subtree>
          </Cost>
          <Plans>
            <Plan>
              <Node-Type>Route</Node-Type>
              <Variant>Scatter</Variant>
              <Startup-Cost>0.00</Startup-Cost>
              <Total-Cost>2002.00</Total-Cost>
              <Plan-Rows>2</Plan-Rows>
              <Plan-Width>32</Plan-Width>
              <ColumnOutputMapping>[-1]</ColumnOutputMapping>
              <OuterParams>[0]</OuterParams>
              <ParamTypes>[text]</ParamTypes>
              <Query>SELECT sum(total_amount) FROM public.orders WHERE created_at &gt; $1::timestamp without time zone</Query>
              <ShardGroupUid>user_data</ShardGroupUid>
              <Cost>
                <Subtree>
                  <NetworkRows>2</NetworkRows>
                  <RemoteCalls>2</RemoteCalls>
                  <Total>2002</Total>
                </Subtree>
              </Cost>
            </Plan>
          </Plans>
        </Plan>
      </Plans>
    </Plan>
  </Query>
</explain>
```

**YAML**

```yaml theme={null}
- Plan:
    Node Type: Aggregate
    Variant: Ordered
    Startup Cost: 0.00
    Total Cost: 2002.00
    Plan Rows: 1
    Plan Width: 32
    Aggregates:
        - 'pg_catalog.sum(sum(total_amount) /*0*/) :: out_0'
    Cost:
        Self:
            RowsProcessed: 2
            Total: 0
        Subtree:
            NetworkRows: 2
            RemoteCalls: 2
            RowsProcessed: 2
            Total: 2002
    Plans:
        - Node Type: Collapse
          Startup Cost: 0.00
          Total Cost: 2002.00
          Plan Rows: 2
          Plan Width: 32
          Cost:
            Subtree:
                NetworkRows: 2
                RemoteCalls: 2
                Total: 2002
          Plans:
            - Node Type: Route
              Variant: Scatter
              Startup Cost: 0.00
              Total Cost: 2002.00
              Plan Rows: 2
              Plan Width: 32
              ColumnOutputMapping: '[-1]'
              OuterParams: '[0]'
              ParamTypes: '[text]'
              Query: SELECT sum(total_amount) FROM public.orders WHERE created_at > $1::timestamp without time zone
              ShardGroupUid: user_data
              Cost:
                Subtree:
                    NetworkRows: 2
                    RemoteCalls: 2
                    Total: 2002
```

The route's `Query` shows the date literal as `$1`. Neki replaces literal
values with parameters before it looks up a plan, as [Query plan cache and
protocols](https://planetscale.com/docs/neki/query-planning.md#query-plan-cache-and-protocols) describes.

## `EXPLAIN (NEKI_PG_PLAN)`

```sql theme={null}
EXPLAIN (NEKI_PG_PLAN, FORMAT TEXT)
SELECT sum(total_amount)
FROM orders
WHERE created_at > '2026-09-09';
```

The route also shows the Postgres plan from every shard in its shard group.

**TEXT**

```text theme={null}
Aggregate [Ordered]  (cost=0.00..2002.00 rows=1 width=32)
  ->  Collapse  (cost=0.00..2002.00 rows=2 width=32)
        ->  Route [Scatter]  (cost=0.00..2002.00 rows=2 width=32)
              Query: SELECT sum(total_amount) FROM public.orders WHERE created_at > $1::timestamp without time zone
              ShardGroup: user_data
              All shard plans
              ->  Shard shard-a  (cost=0.00..0.00 rows=0 width=0)
                    ->  Aggregate  (cost=2.45..2.46 rows=1 width=32)
                          ->  Seq Scan on orders  (cost=0.00..2.40 rows=18 width=5)
                                Filter: (created_at > ('2026-09-09'::cstring)::timestamp without time zone)
              ->  Shard shard-b  (cost=0.00..0.00 rows=0 width=0)
                    ->  Aggregate  (cost=4.17..4.18 rows=1 width=32)
                          ->  Seq Scan on orders  (cost=0.00..4.10 rows=26 width=5)
                                Filter: (created_at > ('2026-09-09'::cstring)::timestamp without time zone)
```

**JSON**

```json theme={null}
[
  {
    "Plan": {
      "Node Type": "Aggregate",
      "Variant": "Ordered",
      "Startup Cost": 0.00,
      "Total Cost": 2002.00,
      "Plan Rows": 1,
      "Plan Width": 32,
      "Aggregates": [
        "pg_catalog.sum(sum(total_amount) /*0*/) :: out_0"
      ],
      "Cost": {
        "Self": {
          "RowsProcessed": 2,
          "Total": 0
        },
        "Subtree": {
          "NetworkRows": 2,
          "RemoteCalls": 2,
          "RowsProcessed": 2,
          "Total": 2002
        }
      },
      "Plans": [
        {
          "Node Type": "Collapse",
          "Startup Cost": 0.00,
          "Total Cost": 2002.00,
          "Plan Rows": 2,
          "Plan Width": 32,
          "Cost": {
            "Subtree": {
              "NetworkRows": 2,
              "RemoteCalls": 2,
              "Total": 2002
            }
          },
          "Plans": [
            {
              "Node Type": "Route",
              "Variant": "Scatter",
              "Startup Cost": 0.00,
              "Total Cost": 2002.00,
              "Plan Rows": 2,
              "Plan Width": 32,
              "ColumnOutputMapping": "[-1]",
              "OuterParams": "[0]",
              "ParamTypes": "[text]",
              "Query": "SELECT sum(total_amount) FROM public.orders WHERE created_at \u003e $1::timestamp without time zone",
              "ShardGroupUid": "user_data",
              "ShardPlans": {
                "All": [
                  {
                    "ShardPlan": {
                      "Shard": "shard-a",
                      "Plan": {
                        "Node Type": "Aggregate",
                        "Strategy": "Plain",
                        "Partial Mode": "Simple",
                        "Parallel Aware": false,
                        "Async Capable": false,
                        "Startup Cost": 2.45,
                        "Total Cost": 2.46,
                        "Plan Rows": 1,
                        "Plan Width": 32,
                        "Disabled": false,
                        "Plans": [
                          {
                            "Node Type": "Seq Scan",
                            "Parent Relationship": "Outer",
                            "Parallel Aware": false,
                            "Async Capable": false,
                            "Relation Name": "orders",
                            "Alias": "orders",
                            "Startup Cost": 0.00,
                            "Total Cost": 2.40,
                            "Plan Rows": 18,
                            "Plan Width": 5,
                            "Disabled": false,
                            "Filter": "(created_at \u003e ('2026-09-09'::cstring)::timestamp without time zone)"
                          }
                        ]
                      }
                    }
                  },
                  {
                    "ShardPlan": {
                      "Shard": "shard-b",
                      "Plan": {
                        "Node Type": "Aggregate",
                        "Strategy": "Plain",
                        "Partial Mode": "Simple",
                        "Parallel Aware": false,
                        "Async Capable": false,
                        "Startup Cost": 4.17,
                        "Total Cost": 4.18,
                        "Plan Rows": 1,
                        "Plan Width": 32,
                        "Disabled": false,
                        "Plans": [
                          {
                            "Node Type": "Seq Scan",
                            "Parent Relationship": "Outer",
                            "Parallel Aware": false,
                            "Async Capable": false,
                            "Relation Name": "orders",
                            "Alias": "orders",
                            "Startup Cost": 0.00,
                            "Total Cost": 4.10,
                            "Plan Rows": 26,
                            "Plan Width": 5,
                            "Disabled": false,
                            "Filter": "(created_at \u003e ('2026-09-09'::cstring)::timestamp without time zone)"
                          }
                        ]
                      }
                    }
                  }
                ]
              },
              "Cost": {
                "Subtree": {
                  "NetworkRows": 2,
                  "RemoteCalls": 2,
                  "Total": 2002
                }
              }
            }
          ]
        }
      ]
    }
  }
]
```

**XML**

```xml theme={null}
<explain xmlns="http://www.postgresql.org/2009/explain">
  <Query>
    <Plan>
      <Node-Type>Aggregate</Node-Type>
      <Variant>Ordered</Variant>
      <Startup-Cost>0.00</Startup-Cost>
      <Total-Cost>2002.00</Total-Cost>
      <Plan-Rows>1</Plan-Rows>
      <Plan-Width>32</Plan-Width>
      <Aggregates>pg_catalog.sum(sum(total_amount) /*0*/) :: out_0 </Aggregates>
      <Cost>
        <Self>
          <RowsProcessed>2</RowsProcessed>
          <Total>0</Total>
        </Self>
        <Subtree>
          <NetworkRows>2</NetworkRows>
          <RemoteCalls>2</RemoteCalls>
          <RowsProcessed>2</RowsProcessed>
          <Total>2002</Total>
        </Subtree>
      </Cost>
      <Plans>
        <Plan>
          <Node-Type>Collapse</Node-Type>
          <Startup-Cost>0.00</Startup-Cost>
          <Total-Cost>2002.00</Total-Cost>
          <Plan-Rows>2</Plan-Rows>
          <Plan-Width>32</Plan-Width>
          <Cost>
            <Subtree>
              <NetworkRows>2</NetworkRows>
              <RemoteCalls>2</RemoteCalls>
              <Total>2002</Total>
            </Subtree>
          </Cost>
          <Plans>
            <Plan>
              <Node-Type>Route</Node-Type>
              <Variant>Scatter</Variant>
              <Startup-Cost>0.00</Startup-Cost>
              <Total-Cost>2002.00</Total-Cost>
              <Plan-Rows>2</Plan-Rows>
              <Plan-Width>32</Plan-Width>
              <ColumnOutputMapping>[-1]</ColumnOutputMapping>
              <OuterParams>[0]</OuterParams>
              <ParamTypes>[text]</ParamTypes>
              <Query>SELECT sum(total_amount) FROM public.orders WHERE created_at &gt; $1::timestamp without time zone</Query>
              <ShardGroupUid>user_data</ShardGroupUid>
              <ShardPlans>
                <All>
                  <ShardPlan>
                    <Shard>shard-a</Shard>
                    <Plan>
                      <Node-Type>Aggregate</Node-Type>
                      <Strategy>Plain</Strategy>
                      <Partial-Mode>Simple</Partial-Mode>
                      <Parallel-Aware>false</Parallel-Aware>
                      <Async-Capable>false</Async-Capable>
                      <Startup-Cost>2.45</Startup-Cost>
                      <Total-Cost>2.46</Total-Cost>
                      <Plan-Rows>1</Plan-Rows>
                      <Plan-Width>32</Plan-Width>
                      <Disabled>false</Disabled>
                      <Plans>
                        <Plan>
                          <Node-Type>Seq Scan</Node-Type>
                          <Parent-Relationship>Outer</Parent-Relationship>
                          <Parallel-Aware>false</Parallel-Aware>
                          <Async-Capable>false</Async-Capable>
                          <Relation-Name>orders</Relation-Name>
                          <Alias>orders</Alias>
                          <Startup-Cost>0.00</Startup-Cost>
                          <Total-Cost>2.40</Total-Cost>
                          <Plan-Rows>18</Plan-Rows>
                          <Plan-Width>5</Plan-Width>
                          <Disabled>false</Disabled>
                          <Filter>(created_at &gt; (&#39;2026-09-09&#39;::cstring)::timestamp without time zone)</Filter>
                        </Plan>
                      </Plans>
                    </Plan>
                  </ShardPlan>
                  <ShardPlan>
                    <Shard>shard-b</Shard>
                    <Plan>
                      <Node-Type>Aggregate</Node-Type>
                      <Strategy>Plain</Strategy>
                      <Partial-Mode>Simple</Partial-Mode>
                      <Parallel-Aware>false</Parallel-Aware>
                      <Async-Capable>false</Async-Capable>
                      <Startup-Cost>4.17</Startup-Cost>
                      <Total-Cost>4.18</Total-Cost>
                      <Plan-Rows>1</Plan-Rows>
                      <Plan-Width>32</Plan-Width>
                      <Disabled>false</Disabled>
                      <Plans>
                        <Plan>
                          <Node-Type>Seq Scan</Node-Type>
                          <Parent-Relationship>Outer</Parent-Relationship>
                          <Parallel-Aware>false</Parallel-Aware>
                          <Async-Capable>false</Async-Capable>
                          <Relation-Name>orders</Relation-Name>
                          <Alias>orders</Alias>
                          <Startup-Cost>0.00</Startup-Cost>
                          <Total-Cost>4.10</Total-Cost>
                          <Plan-Rows>26</Plan-Rows>
                          <Plan-Width>5</Plan-Width>
                          <Disabled>false</Disabled>
                          <Filter>(created_at &gt; (&#39;2026-09-09&#39;::cstring)::timestamp without time zone)</Filter>
                        </Plan>
                      </Plans>
                    </Plan>
                  </ShardPlan>
                </All>
              </ShardPlans>
              <Cost>
                <Subtree>
                  <NetworkRows>2</NetworkRows>
                  <RemoteCalls>2</RemoteCalls>
                  <Total>2002</Total>
                </Subtree>
              </Cost>
            </Plan>
          </Plans>
        </Plan>
      </Plans>
    </Plan>
  </Query>
</explain>
```

**YAML**

```yaml theme={null}
- Plan:
    Node Type: Aggregate
    Variant: Ordered
    Startup Cost: 0.00
    Total Cost: 2002.00
    Plan Rows: 1
    Plan Width: 32
    Aggregates:
        - 'pg_catalog.sum(sum(total_amount) /*0*/) :: out_0'
    Cost:
        Self:
            RowsProcessed: 2
            Total: 0
        Subtree:
            NetworkRows: 2
            RemoteCalls: 2
            RowsProcessed: 2
            Total: 2002
    Plans:
        - Node Type: Collapse
          Startup Cost: 0.00
          Total Cost: 2002.00
          Plan Rows: 2
          Plan Width: 32
          Cost:
            Subtree:
                NetworkRows: 2
                RemoteCalls: 2
                Total: 2002
          Plans:
            - Node Type: Route
              Variant: Scatter
              Startup Cost: 0.00
              Total Cost: 2002.00
              Plan Rows: 2
              Plan Width: 32
              ColumnOutputMapping: '[-1]'
              OuterParams: '[0]'
              ParamTypes: '[text]'
              Query: SELECT sum(total_amount) FROM public.orders WHERE created_at > $1::timestamp without time zone
              ShardGroupUid: user_data
              ShardPlans:
                All:
                    - ShardPlan:
                        Shard: shard-a
                        Plan:
                            Node Type: "Aggregate"
                            Strategy: "Plain"
                            Partial Mode: "Simple"
                            Parallel Aware: false
                            Async Capable: false
                            Startup Cost: 2.45
                            Total Cost: 2.46
                            Plan Rows: 1
                            Plan Width: 32
                            Disabled: false
                            Plans:
                                - Node Type: "Seq Scan"
                                  Parent Relationship: "Outer"
                                  Parallel Aware: false
                                  Async Capable: false
                                  Relation Name: "orders"
                                  Alias: "orders"
                                  Startup Cost: 0.00
                                  Total Cost: 2.40
                                  Plan Rows: 18
                                  Plan Width: 5
                                  Disabled: false
                                  Filter: "(created_at > ('2026-09-09'::cstring)::timestamp without time zone)"
                    - ShardPlan:
                        Shard: shard-b
                        Plan:
                            Node Type: "Aggregate"
                            Strategy: "Plain"
                            Partial Mode: "Simple"
                            Parallel Aware: false
                            Async Capable: false
                            Startup Cost: 4.17
                            Total Cost: 4.18
                            Plan Rows: 1
                            Plan Width: 32
                            Disabled: false
                            Plans:
                                - Node Type: "Seq Scan"
                                  Parent Relationship: "Outer"
                                  Parallel Aware: false
                                  Async Capable: false
                                  Relation Name: "orders"
                                  Alias: "orders"
                                  Startup Cost: 0.00
                                  Total Cost: 4.10
                                  Plan Rows: 26
                                  Plan Width: 5
                                  Disabled: false
                                  Filter: "(created_at > ('2026-09-09'::cstring)::timestamp without time zone)"
              Cost:
                Subtree:
                    NetworkRows: 2
                    RemoteCalls: 2
                    Total: 2002
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
