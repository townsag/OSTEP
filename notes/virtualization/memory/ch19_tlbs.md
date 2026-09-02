## Ch19: Paging: Faster Translations
### Summary:
Paging remedies some of the problems associated with allocating variable sized memory chunks. This includes external fragmentation. This comes at the cost of storage space and runtime. Practical systems use caching to offset these tradeoffs.

Memory addresses are split into the virtual page number and the offset into the associated physical page. The mapping between virtual pages and physical pages is unique to each process. The translation lookaside buffer is a cache built into the memory management unit on the CPU that allows the CPU to store the mapping between popular virtual page numbers and their associated physical frame addresses. Each memory access starts with a lookup into the TLB to convert the virtual memory address to a physical address. On cache miss, a trap instruction is executed so the kernel may retrieve the correct physical page number from the kernel space page table and store that in the TLB. Modifying the TLB is a privileged operation, only the kernel can modify the page table as it is in kernel memory.

In some systems, the TLB is cleared on a context switch so that it may be filled with the address translations for the new process. There are optimizations that allow the TLB to store address translations for more than one process.
### Terms:
- translation-lookaside buffer:
    - physical cache in the CPU MMU (memory management unit) that stores popular virtual page number -> physical page number mappings
    - allows the cpu to skip the main memory access associated with reading the program instruction at a virtual memory address or performing a memory operation
    - manged by the hardware
    - performs protection checks
        - read, write, execute
    - normal page size is a round 4KB
    - valid bit of the TLB entries indicates wether the entry in the TLB for that virtual page number is valid in the context of the currently running process 
        - this is different from the valid bit for a page in the OS page table
            - this indicates wether the physical page has been allocated for this process and virtual page number
            - the OS marks all the entries in the TLB as invalid when a context switch happens because the virtual addresses of the new process will mostly be mapped to different physical addresses
    - address space identifier:
        - small identifier per process stored in each entry of the page table and TLB
        - allows the TLB to persist between context switches because the CPU can now check both the valid bit and the ASI to find a cache hit 
    - fully associative cache:
        - not ordered
        - 32, 64, or 128 entries 
        - each entry has both the virtual page number and the physical frame number, could be anywhere in the cache
- spacial locality:
    - the assumption that when an element in memory is accessed, the adjacent elements in memory are likely to soon be accessed
- temporal locality:
    - the assumption that when an element is memory is accessed, that element is likely to be soon accessed
- who updates the tlb on a cache miss:
    - hardware managed TLB
        - the format of the page table is agreed upon by the os and hardware
        - the hardware can access the page table using the page table base register
        - the hardware manages reading from the page table and writing to the tlb
    - software managed TLB:
        - traps into the kernel whenever a TLB miss occurs
            - raises permission level
        - OS is responsible for reading from the page table and returning the physical page number to the TLB
        - OS uses privileged instruction to update the TLB
        - return from trap instruction at the end to the TLB miss trap handler retries the instruction which caused the initial TLB miss
        - must be certain that the TLB miss handler code does not itself cause TLB misses
            - can store TLB miss trap handler in non-mapped physical memory
            - physical memory can be accessed by the kernel
            - not swapped to disk
        - this decouples the page table implementation from the hardware MMU implementation relative to the hardware managed TLB

### Questions:
- is the page table cleared during context switches or traps into the OS?
    - does populating the page table require trapping into the OS?
        - if so, does this come with extra overhead
            - saving and restoring user program and kernel program register state
    - does the MMU have the ability to access kernel memory space?