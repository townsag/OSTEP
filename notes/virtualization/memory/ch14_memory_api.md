## Ch14: Interlude: Memory API
### Summary:
The api that the programer uses to access memory includes a way to create and destroy memory resources. Programmers call malloc to with the size of the memory they want to access and receive a pointer to allocated memory of that size. 

When they are done with the memory, the program is responsible for explicitly deallocating the allocated memory using free. That is a routine which takes the pointer to allocated memory and returns it to the operating system.
### Terms:
- stack memory:
    - managed automatically by the compiler
    - used for local variables and for arguments and return values of functions
- heap memory:
    - managed explicitly by the programmer
    - allocated manually as part of the code
- malloc:
    - a function provided by the c standard library to allocate heap memory
    - returns a pointer on success and null (zero) on failure
- free:
    - a function provided by the c standard library that releases the memory that has been allocated using malloc
        - this memory may be used again in this process for something else
    - takes the pointer to allocated memory provided by malloc as input
- common errors:
    - unallocated buffer:
    - buffer overflow:
    - reading uninitialized memory
    - memory leak
    - dangling pointer
        - either the allocated memory is released back to the OS, then using that memory is a segfault
        - or that memory has been kept by the process but is re allocated for a different variable, causing the read to get bad data
    - double free
- role of the OS:
    - the os allocates chunks of memory to the process, all of which is cleaned up when the process exits
    - within the process the malloc library manages which memory has been claimed. It may also request more memory from the OS 
### Questions: