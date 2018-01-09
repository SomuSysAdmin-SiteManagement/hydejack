---
layout : post
title : 'Working with journald'
tags : [RHCSA, RHCSA_mod, Configuring Logging, Working with journald]
categories: [RHCSA]
---


Everything that *journald* is doing is written to a binary file. The
file can be explored using two methods: by using **systemctl** and
**journalctl**. Journald integrates very well with systemctl and thus,
systemctl commands can get information from journald and vice versa.
Using `systemctl status <serviceName>` gives us the log information that
systemctl receives from the journald environment.

## journalctl

The `journalctl` command opens up the binary file that journald is
writing to. Since it’s a large file, there are several filtering
options:

### journalctl -b

The `journal -b` command only shows us the boot log.

``` console
# journalctl -b
-- Logs begin at Sat 2017-12-02 15:25:40 IST, end at Mon 2017-12-04 13:23:04 IST. --
Dec 02 15:25:40 vmPrime.somuVMnet.com systemd-journal[86]: Runtime journal is using 8.0M (max allowed 289.5M, trying to leave 434.3M free of 2.
Dec 02 15:25:40 vmPrime.somuVMnet.com kernel: Initializing cgroup subsys cpuset
Dec 02 15:25:40 vmPrime.somuVMnet.com kernel: Initializing cgroup subsys cpu
Dec 02 15:25:40 vmPrime.somuVMnet.com kernel: Initializing cgroup subsys cpuacct
Dec 02 15:25:40 vmPrime.somuVMnet.com kernel: Linux version 3.10.0-693.5.2.el7.x86_64 (builder@kbuilder.dev.centos.org) (gcc version 4.8.5 2015
Dec 02 15:25:40 vmPrime.somuVMnet.com kernel: Command line: BOOT_IMAGE=/vmlinuz-3.10.0-693.5.2.el7.x86_64 root=/dev/mapper/centos-root ro crash
Dec 02 15:25:40 vmPrime.somuVMnet.com kernel: Disabled fast string operations
...
```

One of the best features of journald is that systemd initiates it
immediately during boot and thus logs about what happens even during the
very first stages of RHEL boot is available.

### journalctl –since=\<time\>

The journalctl has a method to filter all results to show us what has
happened since a specified period where only the logs written after a
certain period are show.

``` console
# journalctl --since=yesterday
-- Logs begin at Sat 2017-12-02 15:25:40 IST, end at Mon 2017-12-04 13:28:03 IST. --
Dec 03 23:04:36 vmPrime.somuVMnet.local systemd[1]: Time has been changed
Dec 03 23:04:36 vmPrime.somuVMnet.local NetworkManager[834]: <info>  [1512322476.5889] audit: op="sleep-control" arg="off" pid=3311 uid=0 resul
Dec 03 23:04:36 vmPrime.somuVMnet.local systemd[1]: Stopping LSB: Bring up/down networking...
...
```

### journald -u

The `journal -u` command shows us all the logs corresponding to a
certain process.

``` console
# systemctl status atd -l
atd.service - Job spooling tools
Loaded: loaded (/usr/lib/systemd/system/atd.service; enabled; vendor preset: enabled)
Active: active (running) since Sat 2017-12-02 15:26:18 IST; 1 day 22h ago
Main PID: 1224 (atd)
CGroup: /system.slice/atd.service
1224 /usr/sbin/atd -f

Dec 02 15:26:18 vmPrime.somuVMnet.local systemd[1]: Started Job spooling tools.
Dec 02 15:26:18 vmPrime.somuVMnet.local systemd[1]: Starting Job spooling tools...
# journalctl -u atd
-- Logs begin at Sat 2017-12-02 15:25:40 IST, end at Mon 2017-12-04 13:37:28 IST. --
Dec 02 15:26:18 vmPrime.somuVMnet.local systemd[1]: Started Job spooling tools.
Dec 02 15:26:18 vmPrime.somuVMnet.local systemd[1]: Starting Job spooling tools...
```

Both systemctl and journald are intimately interconnected. Journald
receives it’s original logging information from systemctl, while the
information displayed by the `systemctl status` command is derived from
the information stored by journald. To see the information in even more
detail, we use the command `journalctl -u <processName> -o verbose`.

``` console
# journalctl -u atd -o verbose
-- Logs begin at Sat 2017-12-02 15:25:40 IST, end at Mon 2017-12-04 13:44:14 IST. --
Sat 2017-12-02 15:26:18.215379 IST [s=7e5f5839...6e;i=8f2;b=f7106...c3;m=25810b8;t=55f58..dd;x=5e9c...46]
PRIORITY=6
_UID=0
_GID=0
_BOOT_ID=f7106b6e5bc144bcac5827c5089f23c3
_MACHINE_ID=9d29aa554cfa4853b59f2d517a8470bd
SYSLOG_FACILITY=3
SYSLOG_IDENTIFIER=systemd
...
```

The above command gives us complete information about the environment of
the process.
