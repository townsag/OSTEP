## Ch10: Multiprocessor Scheduling (Advanced)
### Summary:
The CPU cache is both a physical piece of hardware and an abstraction that allows the cpu to act as if it has very fast access to all of main memory. Context switches between different processes sometimes requires evicting and repopulating entries from the cache, this is slow. Furthermore, multiprocessor scheduling is an inherently parallel problem. The core issue is, how do we schedule processes and threads such that we can minimize cache evictions and coordination.

In the case of MQMS, this is accomplished by making the queues for each processor independent. As a consequence of having independent queues, some processors may have significantly less work to do than other processors, this is called load imbalance. This can be remedied by queues periodically stealing work from other queues that are more burdened. Within a queue, jobs are scheduled using any of the scheduling algorithms described in the previous chapter, adapted to maximize cache affinity.

Real life systems 
### Terms:
- cache:
    - proportionally small piece of memory that stored data popular to the cpu
- locality:
    - temporal locality states that recently used data is likely to be used again soon
    - spacial locality states that when data is read from memory the adjacent data will also be used soon
- cache coherence:
    - the cache should hold correct data that is useful to the currently running process
        - these are sometimes conflicting requirements
        - how can we maximize the usefulness of the data in the cache while maintaining correctness
- cache affinity:
    - a process builds up state in a cache, this includes values that need to be written out to memory for a write back cache and values that will be read again
    - processes will run faster if scheduled on the same CPU as they were originally running on
- Single Queue Multiprocessor Scheduling:
    - use an existing single process scheduler that uses some sort of policy decision to create an ordering of executions of processes, then apply that ordering two cpus instead of one cpu
    - does not account for cache affinity, may be quite slow to load state into the caches
    - requires exclusive sequential access to the head of the queue
        - schedulers are inherently parallel in a system that has many cpu cores. If the act of scheduling requires locking some queue data structure, then only one scheduling decision can be made at a time
- Multi Queue Multiprocessor Scheduling:
    - each CPU core gets it's own queue. Scheduling decisions are made independently for each core without accounting for other cores
    - processes are only ever run on one core
    - load imbalance:
        - independent queues lead to skewed workloads between queues. One queue might have more work to do than another queue. Or one queue might have different types of work to do than another queue
    - work stealing:
        - an approach that can mitigate load imbalance
        - when a queue + cpu pair see that it has less work to do than another queue, it can steal / migrate some of the work from the other queue so that it may balance the workload
        - there should be some work stealing threshold or timeout to prevent thrashing
### Questions:
