## Ch9: Scheduling: Proportional Share
### Summary:
The goal of a fair scheduler is to share the cpu proportionally between all the running processes. The mechanisms accomplishing this make use of randomness and book keeping.

Lottery based scheduling randomly picks a process to run given a set of weighted priorities. This comes at the tradeoff of poorly approximating random scheduling for short processes but requiring very little resource utilization.

Stride scheduling keeps track of how much of the cpu resource each process has used so far, weighted by that processes priority. The process with the lowest resource consumption is run at each scheduling decision. This comes with the upside of being fair, however it requires more book keeping / global state than lottery based scheduling. Furthermore, it requires the system admin to choose a time slice, which cannot be set correctly for all workloads.

Linux CFS achieves fair scheduling over a period by dividing the cpu usage between each process weighted by that processes priority. Processes are run over that time slice in order of prior utilization of the cpu ascending (the same process may be run twice in a row if no other process has lower utilization). A minimum per process period is used to prevent too much overhead from context switching. The record of the resources that a process has consumed as well as the time allotted to a process can be weighted by the processes priority. This comes at the tradeoff of requiring memory to store global process state and cpu time to determine which process to run next. 

### Terms:
- tickets:
    - this is a logical representation of the allotment of a system resource that a process has access to
- lottery based scheduler:
    - keeps a record of all the processes and their alloted tickets
    - holds a lottery to determine which process is next by picking a number and running the process with the ticket range including that number
    - random lottery scheduling is simple and lightweight
    - fairness metrics approaches 1 as the length of the job increases
    - non deterministic fair share scheduling
- stride scheduling:
    - deterministic fair share scheduling
    - stride: 
        - this is a number that represents how much of the processes cpu resource budget is consumed each time the process runs
        - inversely proportional to the number of tickets that a process has
        - intuitively like the length that a process travels in one step towards the finish line
    - pass:
        - each time the process runs for a time step, we increment the pass by the stride
        - the pass is the record of the resource budget that the process has consumed so far
    - at any point in time, run the process with the lowest pass value for one time step
    - new processes may dominate the cpu if they are added with 0 pass
    - requires global stat
- Linux completely fair scheduler:
    - virtual runtime:
        - generally proportional to actual runtime of a process
    - when a scheduling decision must be made, pick the process with the lowest virtual runtime to run
    - scheduling latency:
        - configurable time slice
        - the period of time over which the schedule will be fair
        - actual time slice is the scheduling latency divided by the number of processes
    - minimum granularity:
        - the smallest time slice the scheduler will run a process for
    - divide each scheduling latency period into as many time slices as you have processes. Then schedule each process for one of those time slices in the order of lowest to highest virtual runtime per process
    - weighting the priority of a process results in that process being allotted time proportional to the weight of that process and consuming vruntime inversely proportional to the weight of that process
    - starvation is prevented by assigning the vruntime value of 0 to new processes or processes that have been sleeping for a while and recently awakened
- fairness metric:
    - for two jobs of the same length and the same priority, a fairness metric could be the time the first job completes divided by the time the second job completes
        - higher is better
    
### Questions:
- still wondering how scheduling interacts with multithreading and io