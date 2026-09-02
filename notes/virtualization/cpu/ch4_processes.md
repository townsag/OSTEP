## Ch 4: The Abstraction: The Process
### Summary:
A process is an abstraction provided by the operating system that represents the logical boundary around a program. A process includes: stack and heap (memory), register state (cpu), and operating system metadata describing the processes relationship with resources. Processes can be managed externally. The OS schedules processes based on their state and interactions with resources in the form of events. 
### Terms:
- process:
    - a running program
    - a summation of the machine state associated with the running program:
        - registers
        - address space
        - os level metadata about which system resources the process is accessing like files in the filesystem
    - either running, ready or blocked

- context switch:
- address space:
    - the memory that a program can access
- process list:
    - an os level data structure that holds information about all processes currently managed by the OS
### Questions: