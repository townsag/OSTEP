## Ch18: Paging
### Summary:
Instead of allocating contiguous variable sized chunks of memory to processes with mechanisms for those chunks of memory to grow, the OS can allocate discrete fixed sized chunks of memory to processes with an indirection layer that makes them seem contiguous.

The crux is allowing the process to access physical memory through this virtual memory abstraction without suffering from space overhead from storing the indirection layer or time overhead from performing the indirection.
### Terms:
- paging:
    - dividing a resource like memory into fixed size segments so that these segments may be allocated to a process
- page frames:
    - fixed size slots that physical memory is divided into
    - fixed size memory slots with no requirements about their placement in physical memory frees up the OS to allocate virtual memory where convenient in physical memory
        - no external fragmentation
    - allows us to support the abstraction of a virtual memory space:
        - we will not have to know which direction memory will grow or the maximum length that it will grow as part of the allocation process
        - pages in virtual memory do not have to be contiguous in physical memory because of indirection
            - this makes resource management easier for the OS
- page table:
    - this is a per-process data structure maintained by the OS that maps virtual pages to physical pages in memory
        - virtual frame number to physical frame number
    - these are called address translations
    - each process has their own page table because there may be overlap in the virtual addresses used by each process
        - the same virtual address may map to different physical addresses depending on the process
    - virtual addresses are split into a portion that addresses the page (virtual page number) and another portion that represents the offset into the page
    - only the virtual page number gets translated to a physical page number, the offset stays the same
- linear page table:
    - simplest implementation of the page table
    - array where each index of the array holds the physical page number associated with that virtual page number index
    - valid bit:
        - each entry in the page table has a bit representing wether or not that physical page has been allocated
        - this allows the virtual memory space to be complete and contiguous while the physical memory space is sparse and incomplete
        - valid bit is set when a physical frame is allocated for that virtual page number
    - protection bits:
        - stores access control rules for the page (read, write, execute, etc.)
    - present bit:
        - does this page reside in physical memory or has it spilled to disk
        - called swapped out
    - dirty bit:
        - has this page been modified since it was read into memory from disk
    - page table entry structure is determined by hardware instruction set architecture
    - page table base register:
        - hypothetical mechanism by which the hardware could access the base of the page table for address translation
- execution:
    - when running a program, the instructions at the instruction pointer must be read into the cpu so that they may be processed
        - one memory access to the page table is needed to map the virtual address of the instruction to the physical address of the instruction
        - a second memory access is needed to read the instruction from the physical address
    - when executing an instruction with a memory access
        - one memory access to the page table is needed to find the physical address of the memory access
        - another memory access is needed to perform the operation
### Questions:
- does performing an indirection on every read to memory make the system slower?
- where are page tables stored? If they are only stored in kernel space then every memory access would be a trap, which would be very expensive.
- does kernel memory use paging to store the page table?
    - does the page table have to use paging to manage itself?
- do we have a chicken and the egg problem?
    - if the page table is stored in privileged os memory then do we have to use the page table to access the page table?
- specifically what mechanism prevents the programmer from accessing the page table base register to modify the process page table?