# Process
- a process is a running program from resource management point of view
- one CPU core can only run one process at a time, but we want to run infinite processes "simultaneuously". 
- how to create the illusion that there are infinite CPUs to run infinite processes at the same time? virtualization!
- virtualization is achieved by CPU **timesharing** made possible by context switch (mechanism) and policy
**note it is a good design pattern to separate the how vs which/what**
- the machine state of a process: registers, memory (address space).
- process creation
    - load static code into memory (lazy)
    - initialize stack and populate some variables like args
    - I/O set up (open file descriptors stdout, etc.)
- process state
    - runnable, running, blocked, zombie etc.
- process list
    - keep track of process state 
    - machine state
        - open file descriptors
        - registers
        - memory regions
# Process API
## fork
creates an almost exact copy of the current process (same in terms of machine state - virtual memory, registers, etc.) EXCEPT the return value from the fork func call (parent returns PID of child, child returns 0).
Note that the programming pattern here is similar to kernel functions in GPU programming. fork launches a bunch of threads running the same code, but we can identify the specific thread by accessing an ID in the current execution context (local/envvar).
## wait
wait for the child process
## exec
- transforms the current process into the new process
- loads the new program in memory, reiniliazes the resources/registers, and proceeds execution the new code.
- Thus exec never returns.
- There are a number of process-level properties that do not change during the process (e.g. processId, open fd, working dir etc.)
## why?
alternatively you make one call to create a process that runs another program, but the separation of process creation (fork) and run another program (exec) allows some prep work to be done before exec such as file IO.
Note that there might be perf concern here as creating a new process running a new program always needs a copy of old process image via fork. If you are calling exec right after forking, you can call vfork. It runs the new forked process on shared memory with old process before execing, thus saving a copy, but it is dangerous.
## redirection
- fork
- close stdout_fileno
- open file.txt, system assigns the smallest fd (stdout_fileno)
- exec
note that fd persists across exec
## pipe
- returns 2 fds
- data is written to kernel buffer
- when buffer is full, writer is blocked until space is available. when buffer is empty, reader is blocked until space is available
## signal

## limited direct execution
The issue is there is only one CPU and when it runs user code via time sharing, it cannot run OS code. How can we make sure OS still has reasonable control over user code even when OS code is not always being executed?
Solution: Run user code directly on CPU (for best performance), but set up the hardware environment such that 
- when CPU runs user code, it is in user mode, and has limited access to resources
- in order to perform system critical operations, it needs to make system calls, which
    - saves regs into kernel stacks **how**
    - calls trap with syscall_num to raise to kernel mode (start running OS code)
    - look up syscall_num in trap table to jump to the system call code to execute (running OS code in priviledged kernel mode to access resource)
    - returns from trap to go back to user mode
- timer interrupt so that process in user mode cannot keep running. interrupt traps into kernel mode (using similar mechanism as syscalls) and maybe context switch (**need to understand the code**) to another process by
    - save the regs of current process to its proc_struct
    - restore the regs of next process from its proc_struct
note we set up trap table at boot time in kernel mode
# scheduling
## set up
workload types
- short interactive workload
    - low response time is the key
    - yields frequently to do IO
- long CPU-intense workload
scheduling metrics
- tournaround time: time from job scheduled till it completes
- response time: time from job scheduled till the job first gets run
- fariness
## shortest job first
If all jobs arrive at the same time and job lengths are known, the optimal solution (**proof?**) for minimizing turnaround time is to run the shortest job first till completion and the second shortest job etc.
If jobs don't arrive at the same time, we use a variant called shortest time to completion first - every time a new job enters, the scheduler preempt and determines which remainig jobs are shortest to complete and do that first.
## round robin
RR runs a job for time slice (multiple of timer-interrupt) and switch to the next and go around.
This approach is completely fair and good response time, but really bad tournaround time
## fairness tournaround time trade off
Either being unfair and run the shortest job to completion first at the cost of response time
Or use RR to lower response time at the cost of turnaround time
## I/O
When a job issues an I/O, the task is blocked, we better overlap the CPU usage with another job to improve utilization. In a SJF system, we would treat each job segment before issuing I/O as a sub-job and schedule them independently.





