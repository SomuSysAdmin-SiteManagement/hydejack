---
layout : post
title : 'Getting process information with ps'
tags : [RHCSA, RHCSA_mod, Managing Processes, Getting process information with ps]
categories: [RHCSA]
---


  - Shows a snapshot of all the running processes.

  - `ps` shows only user’s own processes.

  - `ps aux` shows the processes of all users with the following
    details:
    
    <span>cM<span>0.75</span></span> **Option** &**Description**  
    **a** \&Show processes for all users  
    **u** \&Display the process’s user/owner  
    **x** \&Also show processes not attached to a terminal  
    
    To see only the first 10 processes, use:
    
    ``` console
    # ps aux | head
    USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
    root          1  0.0  0.3 128164  6852 ?        Ss   Nov28   0:16 /usr/lib/systemd/systemd --switched-root --system --deserialize 21
    root          2  0.0  0.0      0     0 ?        S    Nov28   0:00 [kthreadd]
    root          3  0.0  0.0      0     0 ?        S    Nov28   0:09 [ksoftirqd/0]
    root          5  0.0  0.0      0     0 ?        S<   Nov28   0:00 [kworker/0:0H]
    root          7  0.0  0.0      0     0 ?        S    Nov28   0:00 [migration/0]
    root          8  0.0  0.0      0     0 ?        S    Nov28   0:00 [rcu_bh]
    root          9  0.0  0.0      0     0 ?        R    Nov28   0:42 [rcu_sched]
    root         10  0.0  0.0      0     0 ?        S    Nov28   0:02 [watchdog/0]
    root         12  0.0  0.0      0     0 ?        S    Nov28   0:00 [kdevtmpfs]
    ```
    
        

      - The **PID** is the Process ID assigned to each process
        automatically by the Kernel. The PID 1 process is Systemd, which
        is the first process started by the kernel, which in turn starts
        all other processes.
    
      - **%CPU** and **%MEM** are the CPU and Memory usage stats. *VSZ*
        is the Virtual Memory (total memory that the process has access
        to) while Resident Set Size (*RSS*) refers to the Physical
        Memory being used by the process. *Note that this includes the
        dynamic libraries, so if a library is used by a module multiple
        times, the memory used by the process will be smaller than the
        RSS*.
    
      - **TTY** represents the terminal number on which the process is
        running. For background process, the TTY will be shown as `?`.
    
      - **STAT** is the status of the process. *S* indicates the process
        is asleep.
    
      - **TIME** is the total runtime of the process.
    
      - **COMMAND** is the command that was executed.

Normally, we use `ps aux` to get the PID of a process. We can see the
terminal grep itself is running on is set to `pts/0`, which is the gnome
terminal.

## Getting PID of a process

To see the PID of a particular process we simply grep some keyword
related to the process, typically the process name.

``` console
# ps aux | grep packagekitd
root       1680  0.0  0.3 480016  5752 ?        Ssl  Nov28   0:05 /usr/libexec/packagekitd
root      44081  0.0  0.0 112660   976 pts/0    R+   21:04   0:00 grep --color=auto packagekitd
```

The `R` status indicates that the command `grep` was running at the
time.

### w command

The `w` command shows all the logged in users, what they’re doing and
the consequent load on the system.

``` console
# w
20:58:00 up 2 days,  1:09,  2 users,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
somu     :0       :0               Mon09   ?xdm?   1:12m  0.84s /usr/libexec/gnome-session-binary --session gnome-classic
somu     pts/0    :0               13:41    0.00s  0.67s 10.88s /usr/libexec/gnome-terminal-server
```

## Seeing Parent and Child process relation

Sometimes we need to see the relation between the processes, because
when we kill a parent process, the child processes are also killed
automatically\! For that we use the command `ps fax`.

``` console
# ps fax | tail
37204 ?        S      0:00  \_ gnome-pty-helper
37205 pts/0    Ss     0:00  \_ bash
43222 pts/0    S      0:00      \_ su -
43230 pts/0    S      0:00          \_ -bash
43291 pts/0    S      0:00              \_ su - somu
43292 pts/0    S      0:00                  \_ -bash
43465 pts/0    S      0:00                      \_ su
43471 pts/0    S      0:00                          \_ bash
44146 pts/0    R+     0:00                              \_ ps fax
44147 pts/0    D+     0:00                              \_ [tail]
```

