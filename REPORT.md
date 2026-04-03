# Modified-xv6 Report

## Performance Comparison

* <b>Round-robin (default)</b>

Average running time: 120 ticks<br>
Average waiting time: 19 ticks

* <b>Priority-based</b>

Average running time: 108 ticks<br>
Average waiting time: 22 ticks

* <b>Multi-level feedback queue</b>

Average running time: 114<br>
Average waiting time: 21

Round-robin performs the best, followed closely by MLFQ, and then PBS.

These results have been obtained by the `user/schedulertest.c` benchmark program.

## MLFQ Exploitation

<b>Q: If a process voluntarily relinquishes control of the CPU (eg. for doing I/O), it
leaves the queuing network, and when the process becomes ready again after the I/O, it is​ ​inserted at the tail of the same queue, from which it is relinquished earlier. how could this be exploited by a process?</b>

A: Suppose a very important process is to be run. Ideally, it should be placed in a high-priority queue. However, as each process has a certain time slice, which would be lesser in high-priority queues. After a certain time, it would be pushed to the lowest-priority queue and would not be given the priority it needs.

A solution to this is to make it spend as much time as it can in the highest-priority queue, and then just before its time slice gets over, make it relinquish the CPU and re-insert it in the same queue. In this manner, the process can exploit this feature to remain in the highest-priority queue despite the time slice.