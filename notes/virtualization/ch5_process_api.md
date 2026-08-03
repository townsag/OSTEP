## Ch 5: Interlude: Process API
### Summary:
The process API provides the system calls fork and exec to interact with processors. fork() creates a new process with the same memory sate (instructions and data), register (program counter), and os metadata (file descriptor) except that the return value of fork is different. Exec updates the instructions and data for the current process but keeps the os metadata.

Processes can be controlled externally using signals.

Only some users can send some signals to processes 
### Terms:
- wait
    - given a pid wait for it to exit or wait for all child processes
- pipe
    - this is a channel that exists in memory between two different processes. Data flows through some OS level buffer. I wonder if it is bounded and has back-pressure
### Questions:
- What is meant by "open file descriptors are kept open across exec() calls"?
    - what happens when two processes have access to the same file descriptor?
    - are file descriptors scoped to a process?