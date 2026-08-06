## Ch8: Scheduling: The Multi-Level Feedback Queue
### Summary:
The OS scheduler does not have omniscient knowledge of the finish time of a job, but it wishes to execute as if it does. A scheduler with omniscient knowledge would be able to minimize turnaround time by running the shortest jobs first and minimize response time by running interactive jobs first. Furthermore, it would be able to account for the changes in behavior that some jobs will undergo.

The multi level feedback queue approximates these characteristics by assuming that processes will be interactive and running interactive processes eagerly. As the scheduler identifies processes that are long running, they are scheduled with lower priority. Finally, the priority level of processes is periodically increased to ensure that all processes get a chance to run.

This approximates shortest job first.

### Terms:
- multi level feedback queue:
    - a series of hierarchically organized queues
    - the jobs in the same queue are at the same priority level
    - jobs can only be queued in one level at a time
    - rules:
        - run the job in the highest queue level
        - if there are multiple jobs in the queue with the same level, run them in all with round robin scheduling
            - this results in high responsiveness for high priority jobs
        - new jobs are placed at the highest priority level
        - once a job uses up it's allotment at a given level of the queue, it is moved down by one priority level
        - after some time, move all jobs in the system to the top level of priority
            - this prevents starvation if there are too many short lived jobs
            - this accounts for the change in behavior of jobs
    - the scheduler varies the priority associated with a job based on the behavior of the job
        - jobs that frequently relinquish the cpu back to the scheduler are kept at high priority
        - jobs that infrequently relinquish the cpu have their priority reduced 
- allotment:
    - the time a job can spend at a given priority level before it's priority is reduced

### Questions:
- How does this work in the case of long running processes that have an asynchronous relationship with the disk?
    - can a process publish many IO requests across many threads and wait on all of them?
    - What if one thread is blocked but another thread isn't
        - I understand that threads have their own registers and stack, are they scheduled separately?