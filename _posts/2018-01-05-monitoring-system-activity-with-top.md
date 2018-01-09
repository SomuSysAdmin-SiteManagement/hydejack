---
layout : post
title : 'Monitoring System Activity with top'
tags : [RHCSA, RHCSA_mod, Managing Processes, Monitoring System Activity with top]
categories: [RHCSA]
---


**top** shows us the top active processes on the system in real-time.

``` console
# top
top - 12:24:09 up 2 days,  6:27,  2 users,  load average: 0.15, 0.07, 0.06
Tasks: 207 total,   2 running, 205 sleeping,   0 stopped,   0 zombie
%Cpu(s):  3.9 us,  0.7 sy,  0.0 ni, 95.4 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1867024 total,   196468 free,   957244 used,   713312 buff/cache
KiB Swap:  1953788 total,  1953788 free,        0 used.   667488 avail Mem

PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
2023 somu      20   0 1943348 246224  49292 S  5.3 13.2  61:53.62 gnome-shell
2250 somu      20   0  525584  12240   5984 S  2.3  0.7   0:00.61 tracker-store
1367 root      20   0  291800  35340  10312 S  1.3  1.9   2:53.91 X
48709 root      20   0  157716   2284   1536 R  0.7  0.1   0:00.06 top
764 root      20   0  305080   6140   4768 R  0.3  0.3   6:45.26 vmtoolsd
2270 somu      20   0  385944  19688  15236 S  0.3  1.1   6:02.21 vmtoolsd
2290 somu      39  19  637720  11280   8036 S  0.3  0.6   0:00.52 tracker-miner-f
48449 root      20   0       0      0      0 S  0.3  0.0   0:00.80 kworker/0:0
1 root      20   0  128164   6852   4084 S  0.0  0.4   0:18.42 systemd
2 root      20   0       0      0      0 S  0.0  0.0   0:00.22 kthreadd
3 root      20   0       0      0      0 S  0.0  0.0   0:11.45 ksoftirqd/0
5 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H
7 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0
8 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcu_bh
9 root      20   0       0      0      0 S  0.0  0.0   0:48.31 rcu_sched
10 root      rt   0       0      0      0 S  0.0  0.0   0:02.52 watchdog/0
12 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kdevtmpfs
13 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 netns
14 root      20   0       0      0      0 S  0.0  0.0   0:00.22 khungtaskd
15 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 writeback
16 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kintegrityd
17 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 bioset
18 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kblockd
19 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 md
25 root      20   0       0      0      0 S  0.0  0.0   0:00.20 kswapd0
```

If we put 3 processes that all run `dd if=/dev/zero of=/dev/null` on the
runqueue, and then execute the `top` command, and then press the `1`
key, all the individual CPU loads are displayed. Since our VM has only
one CPU, it shows up as *Cpu0*.

``` console
# top
...
%Cpu0  : 27.6 us, 72.4 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
...
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
48739 root      20   0  107948    612    516 R 32.9  0.0   0:04.59 dd
48737 root      20   0  107948    612    516 R 32.2  0.0   0:06.47 dd
48738 root      20   0  107948    608    516 R 32.2  0.0   0:04.94 dd
```

The first line of the output of the `top` command is the same as that of
the `uptime` command. The top command also shows the Free and available
memory in both physical RAM as well as Swap.

Now since there is only one cpu available, the 3 busiest processes, (the
`dd` commands) have evenly distributed the CPU cycles among them
(assigned by the scheduler), nearly 33% each (the rest is overheads and
cycles used by other processes).

`%Cpu(s): 26.5 us, 73.5 sy, 0.0 ni, 0.0 id, 0.0 wa, 0.0 hi, 0.0 si, 0.0
st`

The CPU line shows the CPU Usage stats in percentages. Here, we see the
CPU usage summary \[Cpu(s)\], the *us* stands for user-space, or
processes started by the user. The *sy* shows us the system space CPU
Usage, and is typically programs that directly deal with hardware. Since
we’ve used the `dd` command that’s directly copying data from one device
to another, we have a high system space CPU usage.

The *id* stands for idle and shows us the percentage of time the system
is idle. The *wa* shows the percentage of time the system is waiting for
I/O operation completion, such as a slow disk or network.
