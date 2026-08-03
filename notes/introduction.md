## Ch 2: Introduction
### Summary:
The operating systems is created to solve these problems in no particular order. Users want access to system resources in a way that is fair, easy, and safe. System resources includes physical resources like: disk, memory, and cup. Users should be protected from each other and the operating system should be protected from users.

### Terms:
- Virtualization:
    - the creation of an abstraction that wraps a physical resource 
    - this is used for encapsulation of complexity
    - the cpu is virtualized using the scheduler and context switching
        - concurrency tools allow parallel programs to deal with the fact that they are not running sequentially
    - memory is virtualized by giving each process a logical memory space which maps to physical memory
        - processes can write to memory as if it was a contiguous array of bytes instead of having to know about it's physical implementation
- system calls:
    - apis function provided by the operating system that allow the programmer to interact with the operating system and operating system managed resources

### Questions:
- how is parallelism handled at the file system level
    