## Ch20: Beyond Physical Memory: Mechanisms
### Summary:
Providing the abstraction of a virtual memory space that is larger than physical memory to many concurrently running processes sometimes requires more memory that is available in physical memory. To address this limitation, allocated pages of memory can be swapped to the disk when the corresponding process is not using them.

The location of a page (disk or memory) is indicated using the present bit. When a virtual memory address is accessed with a TLB cache miss, the MMU may find that the requested page is swapped to disk. In this case, a page fault is raised and the OS page fault handler executes. The OS moves the swapped page from disk to memory then updates the page table to reflect the change.
### Terms:
- memory hierarchy:
    - small fast memory sits on the cpu like the L1 cache and the TLB in the MMU
    - slightly larger caches are shared between the cpus, the L3 cache
    - main memory is shared between cpus and is backed by pieces of main memory that have been swapped to the disk
    - each process wants the illusion of being able to access all of a virtual address space that is larger than physical memory
        - how do we provide this illusion
- swap space:
    - space reserved on the disk for moving memory pages in and out of memory as they are used
    - addressed using the disk address of a given page
    - divided into page sized units
- present bit:
    - this is a bit in the page table entry that indicates if the requested page is in physical memory or swapped out to disk
    - upon retrieval of the physical frame number on a TLB miss, the MMU will check the the present bit in the page table entry. If the present bit is true, the MMU uses that page table entry to construct the physical address for the memory access
    - if the present bit is false the MMU generates a page fault
- page fault:
    - this is what happens when the requested page has been allocated but is not present in physical memory but is instead on disk
    - triggers the page fault handler
    - should be called a page miss because it is legal behavior
- page fault handler:
    - OS defined code
        - this is true for systems with either software managed TLB and hardware managed TLB
    - the OS can store the disk address of the missing page in the page table
    - steps:
        - read disk address of missing page from page table
        - read the missing page from disk into new PFN in memory
        - update page table present bit and PFN to reflect new location in memory
        - restart the instruction that originally triggered the TLB miss
            - retries the TLB miss code to read the correct address translation from the page table, populate the TLB, and return the correct PFN
    - the process that generated the page fault is blocked while the page fault handler is running and the missing page is being read from disk
        - this is a good time to run other processes
- memory conflicts / memory pressure:
    - reading a new page from memory (paging in) may require removing an old page from memory (paging out) to make room for the new page
    - page replacement policy:
        - the way we decide which page to remove
    - high water mark:
        - imagine that the remaining pages in memory are like water in a cup
        - the high water mark is like an amount of water that we can have in the cup at which point we are satisfied with the amount (of available pages)
        - this is when we stop running the background thread that evicts pages
    - low water mark
        - the amount of water at which we start running the background task that evicts pages from memory
    - swap demon
        - the background that evicts pages from memory
    - if the page fault handler finds that a requested page is valid but not present and there is no place in memory to fit that page, it can start the swap daemon then sleep until notified by the swap daemon that there are available pages
### Questions:
- if pages can move in and out of memory, does their physical frame number change when they are moved?
- what happens if the OS swaps a page out of memory before another process has the opportunity to use that page but after that process has translated a memory address to that physical frame number?
    - is address translation and memory access atomic?
    - are there tlb os race conditions?
- are there races to claim pages freed by the swap daemon?