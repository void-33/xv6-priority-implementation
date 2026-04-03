# Enhanced-xv6

xv6 is a re-implementation of Dennis Ritchie's and Ken Thompson's Unix
Version 6 (v6).  xv6 loosely follows the structure and style of v6,
but is implemented for a modern RISC-V multiprocessor using ANSI C.

## Specification 1: Scheduling Algorithm

```bash
make clean
make qemu SCHEDULER=XXXX [DEFAULT|PBS]
```
The default scheduler of xv6 is round-robin-based. 2 additional scheduling policies have been implemented:

* Priority-based
* Multi-level feedback queue

### 1. Priority-based

This is a non-preemptive priority-based scheduling policy that selects the process
with the highest priority for execution. In case two or more processes have the same priority, we use the number of times the process has been scheduled to break the tie. If the tie remains, use the start-time of the process to break the tie (processes with lower start times are scheduled earlier).

Here, we have static priority and dynamic priority. Dynamic priority varies with running time and sleeping time and decides scheduling. Static priority is used to calculate dynamic priority.

#### Implementation

* Again, we run a for loop to search for the process with the highest priority (lowest dynamic priority).
* To measure the sleeping time, when the process is sent to sleep via `sleep()` in `kernel/proc.c`, the number of ticks is stored in `struct proc::s_start_time`. Then, when `wakeup()` in `kernel/proc.c` is called, the difference between the current number of ticks and the previously stored time is stored in `struct proc::stime` as the sleeping time.
* Only the static priority (60 by default) is stored in `struct proc`. The niceness and the dynamic priority are calculated in the loop, when the process to be scheduled is being selected.
* The call to `yield()` has been conditionally disabled for PBS.
* The `set_priority()` system call can be used to change the static priority of a process. It has been implemented in the same manner as in specification 1. A user program has also been implemented.

```bash
setpriority [priority] [pid]
```

## Specification 2: procdump

`procdump()` prints a list of processes to the console when a user enters <kbd>Ctrl</kbd>+<kbd>P</kbd> on the console.
Here, I have extended this function to print more information about all
the active processes.

* process ID
* priority
* state
* running time (`struct proc::rtime`)
* waiting time (current ticks or `struct proc::etime` - creation time - running time)
* number of times scheduled (stored in `struct proc::no_of_times_scheduled`)
