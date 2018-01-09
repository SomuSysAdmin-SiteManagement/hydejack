---
layout : post
title : 'Understanding Performance Load'
tags : [RHCSA, RHCSA_mod, Managing Processes, Understanding Performance Load]
categories: [RHCSA]
---


When a process is ready for execution, it’s added to the runqueue, where
it awaits evaluation by the scheduler to be assigned to a CPU. The
amount of processes awaiting to be executed determines the performance
load.

![Performance
Load<span label="fig:2"></span>](RHCSA/Mod2/chapters/2.10.a)

## uptime Command

The `uptime` command shows us how long the system has been on, the
number of users logged on and a snapshot of the current CPU demand for
the last 1, 5 and 15 minutes, in that order. However, this number is
represented for the total CPU resources being used, and not an average\!
Thus, for a single CPU system, uptime of 1 = 100% usage, while for a 4
CPU system, it means the CPU has been 75% idle.

The *System Load Average* is the number of processes in the runqueue
that are either in runnable (ready to run/already running) state or in
an interruptible state (e.g., waiting for I/O access).

``` console
# uptime
11:59:13 up 2 days,  6:02,  2 users,  load average: 0.00, 0.01, 0.05
```

The `nproc` command shows us the number of CPU cores available to us.

``` console
# nproc
1
```

