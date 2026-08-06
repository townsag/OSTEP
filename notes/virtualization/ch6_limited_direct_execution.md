## Ch6: Limited Direct Execution:
### Summary:
The OS is a C program running on the CPU, sharing the CPU with the other programs. It is the goal of the OS to ensure that programs can run directly on the CPU for maximal performance while still being controlled in their access to system resources, including the CPU.

User mode programs can access system resources using system calls. These are a mechanism by which user mode processes can perform privileged instructions pre-specified by the OS. System calls use hardware support to access OS code and resources, not context switches.

Fair multiprocessing on the same CPU requires a mechanism by which the OS can preempt running processes and schedule other processes. The hardware timer triggers an instruction similar to a trap. After this, the OS can decide to perform a full context switch. 

### Terms:
- user mode:
- kernel mode:
- trap instruction:
    - this is an instruction which saves the register state of the currently running user program, sets the execution mode to kernel mode, loads the code associated with performing a system level action, executes the system level action, then restores the register state associated with the user program and continues execution of the user program
    - this is notable *not* a context switch
    - this system call uses it's own memory space, the kernel stack and heap
    - the code associated with each system call is populated at boot time, the trap instruction calls some of the pre-populated trap handling code. This is stored at the hardware level in the CPU
- timer interrupt:
    - there is a physical component of the CPU which raises a timer interrupt after some period of time has passed. The timer interrupt results in the execution of the interrupt handler. This is like a trap handler
- context switch:
    - save the general purpose registers and program counter of the currently running process onto the processes kernel stack
    - if the kernel code timer interrupt handler decides that it is time for a context switch, save the registers associated with the os management of this process to the process management data structure
    - restore the context of the kernel management of the new process from the kernel process metadata structure
    - restore the context of the soon to be running process from that processes kernel stack
    - the running process becomes the new process without loading any new memory, each processes memory is separate, only the memory block that is being pointed to changes. The stack pointer for the new process is pulled from the kernel stack for that process and loaded into the stack pointer register

### Questions:
- What is to prevent any arbitrary program from elevating its privilege level?
    - ?
- how does memory spill to disk? What happens if we need the memory associated with a not scheduled process during the execution of another process