## Ch 22: Beyond Physical Memory: Policies
### Summary:
In order to provide the abstraction of a large virtual memory space to many processes, some pages of memory must be occasionally swapped out to disk. This allows processes to allocate virtual memory greater than the size of physical memory and allows many processes to share physical memory. Reading and writing pages of memory to disk is very expensive, so we must carefully choose the best page to evict so that we may minimize the time applications wait for the page fault handler and the number of times the page fault handler is run.

Algorithms to choose the page to evict include FIFO, random, and LRU. Of these, LRU is the best is many cases because of it's use of knowledge of the processes historical memory access behavior. This is highly workload dependent though.
### Terms:
- memory pressure:
    - when there is little free physical memory remaining and processes want to swap memory pages from disk back into physical memory
- replacement policy:
    - our goal in picking a replacement policy is to minimize the number of page faults / cache misses that we experience
- cache miss types:
    - compulsory miss:
        - the first time a value is accessed there will always be a miss for that value, this is not a fault of the cache replacement algorithm
    - capacity miss:
        - when a value was previously in the cache but it had to be evicted to make room for new values
    - conflict miss:
        - an implementation detail of the caching mechanism
        - ex two values hash to the same location
- average memory access time:
    - T(memory) + (P(miss) * T(disk))
    - the time it takes to access memory plus the time it takes to access disk times the probability that we will have to access disk (cache miss)
    - originally I thought that you would have to pay the cost of accessing memory a second time for each page fault but it occurs to me that accessing the page table might be much cheaper than accessing main memory
        - maybe the page table is in the L2 cache?
    - the cost of accessing disk is so high that is significantly skews the average access time
- optimal replacement policy:
    - evict the page that will be accessed furthest in the future from the current moment
    - unfortunately the future is not known
- replacement policies:
    - fifo:
        - when looking at the cache (main memory) for a page to evict, evict the page that has been in memory the longest
        - can be simply implemented with a queue
    - random:
        - select the page to evict from the cache randomly
    - LRU:
        - multiple options for measuring the usefulness of a page
            - frequency of access
            - recency of access
        - use temporal locality
            - recently accessed pages are likely to be accessed again soon
        - has the stack property
            - all the values in an LRU cache of size N are included in an LRU cache of size N+1
            - the hit rate of an LRU cache is monotonically increasing with the size of the cache
        - requires that the OS does work on each memory access so we may record the frequency / recency of use of every page
        - can get hardware support where the MMU updates a time field in the page table but we still have to pay the indirection cost of traversing the page directory to write the time field and the cost of scanning the page table when finding the least recently used page
        - approximating the least recently used page is almost as effective as finding the exact least recently used page
            - clock algorithm:
                - independently of the page table, store an array of bits with each bit recording wether the corresponding page in the page table has been accessed recently by the cpu
                    - store this in the PCB (process control block)
                - upon each memory access, the CPU sets the corresponding bit to true
                - start the os "clock hand" pointer at a random place in the array
                - upon a page fault that requires a cache eviction, the OS linearly scans the array until a page with the used bit not set is found
                    - as the OS scans, it sets all entries to 0
                - the OS chooses that page as the page to evict
    - for a program with a completely random memory access pattern: FIFO, LRU, and random all behave similarly
    - for a machine with as much physical memory as the program needs, the cache replacement algorithm does not matter
    - for a workload with high locality, LRU performs better than either random or FIFO
    - looping sequential workload:
        - iterate sequentially through N pages then start over
            - like running compaction or indexing
        - this access pattern is the worst case scenario for both FIFO and LRU because in each case the next page to be accessed is also the next page to be evicted
        - results in 0% hit rate
- thrashing:
    - a condition that occurs when the current set of scheduled processes requires more memory than is available, processes are constantly overwriting the pages that are requested by each other
    - some solutions:
        - admission control: only schedule a subset of processes that have manageable memory requirements
        - kill processes using too much memory
### Questions: