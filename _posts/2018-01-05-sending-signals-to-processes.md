---
layout : post
title : 'Sending Signals to processes'
tags : [RHCSA, RHCSA_mod, Managing Processes, Sending Signals to processes]
categories: [RHCSA]
---


Signals are mandatory instructions that the process can’t ignore. Some
of the most common Signals are **SIGTERM** and **SIGKILL**. The
`SIGTERM` signal asks a process to cease its activity. If the signal
doesn’t work, i.e., the process doesn’t obey it, then we send the
`SIGKILL` signal, which terminates the process.

We can send these signals by the use of the `top` command. While top is
running, we have to press the `k` key to initiate signal sending via
`kill`. To choose the appropriate process, we enter the PID. The default
signal is `SIGTERM` (a.k.a. Signal 15). The process is terminated
immediately on sending the signal.

When we send Signal 9 (`SIGKILL`) the process doesn’t have time to save
or clean up its work, and thus the execution stops instantaneously\!
This means if a process is working on an open file, the `SIGKILL` signal
can cause irreparable damage to it.

## kill command

To send a signal directly from the command line, we use the `kill`
command. To send a `SIGTERM` signal, we merely provide the PID. To send
a `SIGKILL` signal, we have to provide a signal number of `9`.

``` console
# ps aux | grep dd
...
root      49575 50.3  0.0 107948   608 pts/0    R    13:25
[2]+  Running                 dd if=/dev/zero of=/dev/null &
# kill -9 49575
# jobs
[2]+  Killed                  dd if=/dev/zero of=/dev/null
```

To kill all processes matching a certain process using Signals uses:

``` console
# top
...
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
49747 root      20   0  107948    608    516 R 22.8  0.0   0:05.81 dd
49748 root      20   0  107948    612    516 R 22.5  0.0   0:05.30 dd
49749 root      20   0  107948    612    516 R 22.5  0.0   0:04.91 dd
49746 root      20   0  107948    612    516 R 22.2  0.0   0:06.60 dd
...
# jobs
[1]   Running                 dd if=/dev/zero of=/dev/null \&
[2]   Running                 dd if=/dev/zero of=/dev/null \&
[3]-  Running                 dd if=/dev/zero of=/dev/null \&
[4]+  Running                 dd if=/dev/zero of=/dev/null \&
# killall dd
# jobs
[1]   Terminated              dd if=/dev/zero of=/dev/null
[2]   Terminated              dd if=/dev/zero of=/dev/null
[3]-  Terminated              dd if=/dev/zero of=/dev/null
[4]+  Terminated              dd if=/dev/zero of=/dev/null
```

