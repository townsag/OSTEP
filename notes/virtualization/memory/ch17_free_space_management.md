## Ch17: Free-Space Management
### Summary
Memory provided to the process by the OS must be managed by the process, often using the malloc library. It is the responsibility of the malloc library to manage allocated memory in such a way that is fast and minimizes wasted space. It is also the responsibility of the malloc library to request more memory 

This is accomplished using various allocation strategies, although none of the allocation strategies are perfect. Memory allocators maintain an collection of the unallocated pieces of memory.

Best fit allocation exhaustively searches all available pieces of free memory for the smallest piece of free memory that is large enough to satisfy the request. This requires a longer search but it keeps large pieces of memory whole

Worst fit allocation exhaustively searches all available pieces of memory for the larges piece of memory. This is split to satisfy the request. This requires a longer search but results in fewer small pieces of unallocated memory

First fit linearly traverses free memory chunks until it finds the first chunk that can satisfy the request. This requires less search time but can result in more fragmentation, especially towards the front of the free list. 

Next fit is like first fit but the search for unclaimed  memory starts at the last place that we satisfied a request. This has the benefit of spreading fragmentation across the free list. 

### Terms
- external fragmentation:
    - when the spaces of unused memory between spaces of allocated memory are too small to be useful or allocated to other processes so they are wasted
- internal fragmentation:
    - when the spaces of allocated memory are larger than requested by the process are larger than needed to account for future growth but that growth does not come, thus they are wasted
- compaction:
    - the act of rearranging allocated pieces of memory such that there are no pieces of unused memory between them
- splitting:
    - when a request is made for memory that does not match the size of any free chunk, split an existing chunk. Return the allocated portion of the existing free chunk of memory to the caller, add back the non-allocated portion to the free memory list
- coalescing:
    - when previously allocated portions of memory are freed, merge them with any adjacent free portions of memory
    - this prevents us from ending up with many small adjacent free sections that would not be eligible to satisfy one large allocation
    - the malloc library keeps some metadata in memory preceding the block of allocating memory which indicates the size of the allocated memory, this is used to return it to the free list when it is released by the caller
        - this header is used by the malloc library and not shared with the application
- free list:
    - data structure in the heap created by the malloc library for keeping track of unallocated memory
    - the free list is collocated with allocated memory in the heap
    - each node in the free list has a size and a pointer to the next node in the free list
    - freed chunks of memory are added to the head of the free list even if they do not come first in a contiguous traversal of memory
        - this can lead to a tangle of nodes in the free list
- allocation strategies:
    - best fit:
        - when looking for a chunk of free memory to satisfy a request for memory, choose the chunk of free memory that is smallest without being smaller than the requested size to
        - split that chunk to service the request
        - search is expensive
    - worst fit:
        - find the largest chunk and split that chunk to service the request
        - keep the remaining large portion of the split chunk in the free list
        - requires search
        - can lead to fragmentation
    - first fit:
        - use the first chunk that is large enough to fit the requested amount of memory to satisfy the request
    - next fit:
        - keep track of the last place in the free list that you have allocated memory from
        - when a new request for memory comes in, start at the last place you looked for free space instead of the front of the free space list
        - this prevents the head of the free space list from disproportionately having small amounts of memory allocated there, or internal fragmentation
    - segregated lists:
        - if the profile of memory requests is know beforehand and there are a large number of requests with common requirements, we can use this strategy
        - reserve on region of memory for requests to get fixed size chunks of memory in the size that will be frequently requested
            - dont have to worry about fragmentation
            - dont have to search for a fit if all the requests and chunks are of the same size
    - buddy allocation:
        - allocate blocks that are of size 2^n
        - recursively divide free space by 2 until we find a block of memory that is big enough to serve the request but not big enough to be split in half again
        - internal fragmentation is possible because more memory can be allocated to the user than necessary when the required amount of memory is not a power of 2
        - when memory is returned to the malloc library, it can be recursively merged with adjacent memory
            - no need to traverse a free list because allocated pieces of memory are of fixed size
### Questions
- why does the header contain a magic number?
    - I assume that the program can freely modify the header on accident, the OS will not stop the program from modifying it because it is in the memory allocated to the program
    - if the header is corrupted, the malloc library will be able to tell because the magic number will be different, however, what will it be able to do?