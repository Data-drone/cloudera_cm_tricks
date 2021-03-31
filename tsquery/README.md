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

#### Looking at the memory reserved
`impala_admission_controller_local_backend_mem_reserved` - The is the memory that impala has reserved

Look at it by pool and by node in order to see if we are hotspotting on nodes or bottlenecking on pools

#### Looking at the memory allocated to the pools
`total_impala_admission_controller_pool_max_mem_resources` - This shows how much memory has been allocated to each of the pools

### Yarn Metrics

We focus on live metrics here to get an understanding of what is going on
####
Once all resources are allocated then there is no more to go around and new apps will hang and wait

Allocated Resources:

`allocated_containers` - Shows the currently allocated Yarn Containers. Different applications perform differently.
Spark will release containers when not needed even if it has been set in the spark builder command. 

`allocated_memory_mb` - Show the currently allocated memory.


Available Resources:
Inverse of the allocated resources

`available_memory_mb` - Shows available memory

`available_vcores` - shows available vcores

####

Check on waiting hive and spark containers:

`pending_containers`


#### Hardware level stats

Host Level Stats (cpu);
This includes host level stuff like os
`select cpu_user_rate / getHostFact(numCores, 1) * 100, cpu_system_rate / getHostFact(numCores, 1) * 100, cpu_nice_rate / getHostFact(numCores, 1) * 100, cpu_iowait_rate / getHostFact(numCores, 1) * 100, cpu_irq_rate / getHostFact(numCores, 1) * 100, cpu_soft_irq_rate / getHostFact(numCores, 1) * 100, cpu_steal_rate / getHostFact(numCores, 1) * 100 where entityName=$HOSTID`

can drop the `entityName`  to get everything

Role level stats (cpu):
This is just for cloudera components

`select cpu_user_rate / getHostFact(numCores, 1) * 100, cpu_system_rate / getHostFact(numCores, 1) * 100 where category = role and hostId=$HOSTID`
