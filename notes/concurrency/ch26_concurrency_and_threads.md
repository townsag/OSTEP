## Ch 26: Concurrency: An introduction
### Summary:
The OS and user space programs both require careful timing when performing activities, especially around critical sections. This includes mutually exclusive access to data, as well as deterministic ordering of operations. In order to achieve this, the OS provides a few hardware backed operations that we compose to create synchronization primitives. 
### Terms:
- thread:
    - an abstraction for a single running process
    - like a separate process from other threads:
        - similarities:
            - shares the same address space and data
        - differences:
            - separate program counter and stack
    - a context switch takes place to move between two threads
        - shared heap different stack
        - Thread Control Block
            - when switching between threads A to B the register of thread A is stored in the thread control block, then the register state from thread B is loaded from the thread control block into the registers
            - this is very similar to a context switch between processes, other than that the register holding the page table base is not changed
        - threads store their stack and heap in the same address space
        - the address space is divided into a number of stacks
        - this overhead is inherent in the design of threads
    - state:
        - program counter that tracks the progress of a thread through a program
        - registers
    - states from the perspective of the scheduler:
        - ready
        - running
        - blocked
    - execution ordering of threads is determined by the OS scheduler
    - even on a single cpu core with sequential execution, two threads modifying the same data can race and create incorrect results
        - reading from memory, incrementing the read counter, then writing to memory is not one atomic operation
        - therefore two threads can perform increments without seeing the others increment
        - on a single core cpu, a timer interrupt between when the value is read and written can prevent the thread from seeing increments that happened while it was not scheduled when it gets re scheduled
    - data race:
        - when two threads concurrently and non atomically modify the same piece of data
        - non deterministic results
    - critical section
        - the piece of code executed by multiple threads simultaneously that accesses a shared resource like memory
    - mutually exclusion:
        - the guarantee that only one thread of execution will access the critical portion at a time
    - atomicity:
        - all or nothing
        - all the grouped actions / instructions occurred or none of them occurred
        - the hardware guarantees that single instructions are atomic
            - a context switch cannot interrupt the critical section if the critical section is one atomic operation
    - don't just need to prevent concurrent access to data
    - also need to control the timing of execution of threads such that one thread is guaranteed to wait for the other thread to finish
- parallelism:
    - the ability to simultaneously work on multiple things
    - use multiple threads to perform homogenous work faster
    - delegate slow blocking io to a thread so the main program can continue to make progress while the thread is blocked
        - disk read
        - page fault
        - network request
    - synchronization primitives:
        - 

### Questions:
- could a thread overwrite the stack of another thread?