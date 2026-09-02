## Ch7: Scheduling: Introduction
### Summary:
The scheduler is responsible for determining which jobs are run when. It intends to create schedules the ballance the rate at which jobs complete with the responsiveness perceived by the user.

Scheduling strategies are evaluated using metrics, these include turnaround time and request time. 


### Terms:
- starting assumptions to be relaxed in the future:
    - each job runs for the same amount of time
    - all jobs arrive at the same time 
    - once started, each job runs to completion
        - this assumption is relaxed by adding preemption to the scheduler mechanism
    - all jobs only use the cpu
        - this assumption is relaxed by adding IO
        - when a single threaded process issues a synchronous IO request the process will not complete work until it is done
        - the IO request is a system call that moves into the kernel
        - when the IO completes the hardware raises an interrupt that also moves into the kernel. The OS can decide to perform a context switch from in the kernel when processing the IO interrupt
        - schedule other jobs while a job is idly waiting for the IO to return
    - the run-time of each job is known
- performance metric:
    - measures the overall performance of the system, to be compared with fairness metrics
- turnaround time:
    - the time at which a job completes minus the time at which the job arrives at the system
- response time:
    - the time the job is first scheduled minus the time the job arrived at the system
- FIFO:
    - work on jobs in the order that they arrive
    - fifo can suffer when a large job is queued before a small job
- SJF:
    - work on jobs in the order of time they have left until they complete ascending
    - good for responsiveness
    - if a very long job arrives first and is scheduled without preemption then all short jobs can be stuck behind the long job
- STCF:
    - work on jobs for discrete time slices, after each time slice switch to the job which has the smallest time to completion
    - like SJF with preemption
    - preempt long jobs to run short jobs
- RR:
    - run each job for a fixed time slice in serial
    - context switching has a fixed cost, if the rate at which we rotate between jobs is too high, the context switching cost will dominate execution
    - shorter time slices decrease the response time
    - very high turnaround time because no one job is prioritized
### Questions: