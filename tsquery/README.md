# Useful tsquery

## Tracking Impala

Impala crash course:
- Queries are allocated into resource pools
- Users can be mapped to pools or set their own pools
- Pools have queues that can be capped and are intended to help control memory consumption

### Useful things to track for Impala

Impala Pools

Use `poolName` to filter to specific Impala Queue Pools
`poolName` for a generic install with include user level groupings which can be messy


`queries_spilled_memory_rate` - to see where queries have written to disk if there is a lot could be a bad query or memory setting

```
select queries_spilled_memory_rate WHERE serviceName=IMPALA-1
```

`impala_query_admission_wait_rate` rate
See queries coming into the queue

```
select impala_query_admission_wait_rate WHERE serviceName=IMPALA-1
```

`impala_query_query_duration_rate` - how long queries run for

```
select impala_query_query_duration_rate WHERE serviceName=IMPALA-1
```

`queries_rejected_rate` - rejected queries mean oom or the queue being full if this has been set

```
select queries_rejected_rate WHERE serviceName=IMPALA-1
```


