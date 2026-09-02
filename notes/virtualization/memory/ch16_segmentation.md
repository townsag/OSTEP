## Ch16: Segmentation
### Summary:
Instead of allocating one large fixed chunk of physical memory for code, heap, and stack and translating between virtual addresses and physical addresses using the base and bound pointer, we can allocate variable sized chunks of memory for each of code, heap, and stack. These variable size chunks of memory can grow independently of each other given the needs of the process. This allows less wasted resources to be allocated to a new process because only what is needed is used.

As a result of this, the hardware support must expand. Instead of storing just a base and bounds pointer, the hardware must store, base, bounds, grow direction, and access control for each segment. The hardware can tell what segment a load/store comes from then apply the rules for that segment. Memory address translation happens much the same for each segment as it did for the one large segment, with the exception of the stack. In the case of the stack, the physical memory address is derived by subtracting the offset of the virtual memory address instead of adding. 
### Terms:
- keeping the entire physical address space for a process in memory is very wasteful because there is a portion of memory between the stack and the heap that is not in use by the process
- segmentation:
    - split the vitual address space into logically distinct segments:
        - code, stack, heap
    - provide a different base and bound pointer for each segment 
        - sometimes called base and size
    - this removes the need for large empty swaths of virtual memory between the stack and the heap
    - this relaxes some of our assumptions:
        - each process needs the same amount of memory
        - each process wants a fixed amount of memory
        - each process wants to use a virtual address space that is smaller than physical memory
        - the physical address space of a process is contiguous
    - only virtual memory that is actually used is allocated space in physical memory
    - when computing the physical address from the virtual address and the segment register values, we must find the offset of the virtual address into that code segment, not just the virtual address
        - for example, address 4200 might be the 200th address into the heap, so the physical address is calculated from (heap base) + 200
- segmentation fault:
    - when the hardware detects that a program is attempting to access memory that is outside of the virtual memory space allocated for the corresponding segment
- segment boundaries:
    - the segment of a given requested memory address can be explicitly denoted by using the two most significant bits of an address 
        - 00 for code, 01 for heap, 10 for stack 
        - limits the total size of the address space 
    - the segment of a given requested memory address can be implicitly determined by looking at how it was obtained
        - if the address is the result of addition with the program counter or was read from the code segment, then it is in the code segment
        - if the address is the result of addition with the stack or base pointer then it is in the stack segment
            - the stack pointer denotes the top of the stack, new values are pushed onto the stack within the current functions stack frame
            - the base pointer denotes the start of the current functions stack frame
        - otherwise it is in the heap segment
    - when a segment needs to be resized the os can copy all of the memory in that segment into a separate larger segment then change the base and bound pointers for that segment to the larger segments base and bounds
        - this only works while the process is not running
        - resizing the process heap is a system call 
    - during a context switch, the OS is responsible for saving the segment registers for the deallocated process and restoring the segment registers for the new process
- external fragmentation:
    - when address spaces are created and grow, the OS may have an uneven distribution of address spaces in memory
        - the lack of large contiguous pieces of free memory means that new address spaces cannot be allocated even if there is technically enough space for them
- segment registers
    - a set of registers or a register containing the address to an array which holds information about each segment
        - base: where in physical memory does the segment start
        - size: how large is the segment
        - grows_positive: which direction in virtual memory does this segment grow in
            - the code segment and the heap grow positively while the stack grows negatively 
    - as a consequence of the fact that the stack grows in the negative direction, creating a physical memory address has to work differently for the stack
        - the base value for stack segment actually denotes the __highest__ physical address in the stack segment
        - physical addresses are calculated by adding the negative offset of the address to the base value
        - the negative offset of the address is calculated by subtracting the offset of the address (into the stack segment) from the maximum segment size
            - offset of 3KB and maximum segment size of 4KB means that the physical address is base - 1KB
    - you may be thinking, why not just let the stack grow forwards in physical address space but backwards in virtual address space
        - this would not work because the values in physical memory would be backwards
- protection bits:
    - metadata attached to a segment indicate whether or not a process may read, write, execute data in that segment
    - segments can be set as read only to allow for them to be shared between processes
        - this way the same code segment may be shared between multiple processes
    - hardware must check that an operation does not violate the rules given by protection bits
### Questions:
- How does resizing the heap work for a multi threaded process?
    - when one thread requests more memory, does it have to wait for all the other threads in that process to be de-scheduled? 