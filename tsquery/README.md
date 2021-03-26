# Useful tsquery

## Tracking Impala

Impala crash course:
- Queries are allocated into resource pools
- Users can be mapped to pools or set their own pools
- Pools have queues that can be capped and are intended to help control memory consumption

## Useful things to track for Impala

### Impala Pools

Queries from users are sent into impala pools to run.
Users can either be assigned to pools or users can manually set pools.
We recommend that admins setup three pools with varying RAM limits.

The majority of users should be allocated to the pool with the lowest ram limits.
Number of concurrent queries can then be set higher without saturating the cluster.
For the medium and large queues, users can then manually select them when their queries require that via the: `SET REQUEST_POOL='<pool_name>';` command prior to running their query. 

Use `poolName` in tsquery to filter to specific Impala Queue Pools
`poolName` for a generic install with include user level groupings which can be messy

#### Bad queries with not enough memory
`queries_spilled_memory_rate` - to see where queries have written to disk if there is a lot could be a bad query or memory setting

```
select queries_spilled_memory_rate WHERE serviceName=IMPALA-1
```

#### See demand on pool
`impala_query_admission_wait_rate` rate
See queries coming into the queue

```
select impala_query_admission_wait_rate WHERE serviceName=IMPALA-1
```

#### Check query durations
`impala_query_query_duration_rate` - how long queries run for

```
select impala_query_query_duration_rate WHERE serviceName=IMPALA-1
```

#### Check query rejections
`queries_rejected_rate` - rejected queries mean oom or the queue being full if this has been set

```
select queries_rejected_rate WHERE serviceName=IMPALA-1
```

### Impala Nodes

TBD


