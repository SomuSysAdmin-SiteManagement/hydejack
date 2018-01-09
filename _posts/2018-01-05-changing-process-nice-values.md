---
layout : post
title : 'Changing Process Nice values'
tags : [RHCSA, RHCSA_mod, Managing Processes, Understanding Priorities and Niceness]
categories: [RHCSA]
---


In the output of the `top` command, the *PR* column shows us the
priority of each individual process. Normal processes are started with
the same priority of **20**. There are certain real-time processes as
well, that have a value of `rt` in their priority column.

In case we need to increase or decrease the priority of a process (e.g.,
higher priority to quickly complete a query, or free resources for other
users by lowering priority) we adjust the niceness (*NI*) value of the
process. This process is called *nicing* a process.

The niceness of a process can range from \(-20\) to \(+19\), and the
consequent value of priority is related as : \[PR = 20 + NI\] Thus, the
priority of a process can range from \(0\) (most aggressive) to \(39\)
(nicest). We can adjust the niceness in small increments instead of huge
jumps (setting niceness to \(-20\)) and see if that does our work. If
not, we can change the niceness incrementally till our goals are met\!

## Chaning niceness from top

To renice a process from top, we can simply press `r`, then select the
PID to renice and enter the new niceness value.

## Changing Niceness from command line

We can also set the niceness from the command line, both while first
running the process using the `nice` command, or change the nice value
of a running process using the `renice` command.

``` console
# nice -n 10 dd if=/dev/zero of=/dev/null &
# top | head
top - 15:40:26 up 2 days,  9:44,  3 users,  load average: 1.10, 0.70, 0.71
Tasks: 215 total,   3 running, 212 sleeping,   0 stopped,   0 zombie
%Cpu(s):  9.5 us, 71.4 sy, 19.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1867024 total,   233356 free,   919716 used,   713952 buff/cache
KiB Swap:  1953788 total,  1953788 free,        0 used.   704744 avail Mem

PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
51465 root      30  10  107948    612    516 R 75.0  0.0   2:00.96 dd
1367 root      20   0  290560  34108  10412 S  5.0  1.8   3:22.26 X
2023 somu      20   0 1923992 226836  49300 S  5.0 12.1  66:10.12 gnome-shell
# renice -n -10 51465
51465 (process ID) old priority 10, new priority -10
# top | head
top - 15:42:18 up 2 days,  9:45,  3 users,  load average: 1.41, 0.92, 0.79
Tasks: 215 total,   2 running, 213 sleeping,   0 stopped,   0 zombie
%Cpu(s): 25.0 us, 75.0 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1867024 total,   233440 free,   919632 used,   713952 buff/cache
KiB Swap:  1953788 total,  1953788 free,        0 used.   704828 avail Mem

PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
51465 root      10 -10  107948    612    516 R 64.0  0.0   3:47.16 dd
1 root      20   0  128164   6852   4084 S  0.0  0.4   0:19.34 systemd
2 root      20   0       0      0      0 S  0.0  0.0   0:00.23 kthreadd
```

If however we find that we have to renice processes all the time, we
might want to move some resource-hungry processes to other server(s).
