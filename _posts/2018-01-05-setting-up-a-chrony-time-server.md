---
layout : post
title : 'Setting up a Chrony Time Server'
tags : [RHCSA, RHCSA_mod, Configuring Time Services, Understanding Time on Linux]
categories: [RHCSA]
---


To configure the time on RHEL 7, we need to configure chrony, using the
`timedatectl` command, recently introduced in this version. The current
system time details can be obtained by:

``` console
# timedatectl status
Local time: Fri 2017-12-22 17:27:31 IST
Universal time: Fri 2017-12-22 11:57:31 UTC
RTC time: Fri 2017-12-22 11:57:31
Time zone: Asia/Kolkata (IST, +0530)
NTP enabled: yes
NTP synchronized: yes
RTC in local TZ: no
DST active: n/a
```

If the current time-zone needs to be changed, we can use the command:

``` console
# timedatectl list-timezones
Africa/Abidjan
Africa/Accra
...
Pacific/Wallis
UTC
```

After a new time-zone has been set, it can be verified with `timedatectl
status`. A new time-zone can be set using:

``` console
# timedatectl set-timezone Europe/Amsterdam
# timedatectl status
Local time: Fri 2017-12-22 13:06:02 CET
Universal time: Fri 2017-12-22 12:06:02 UTC
RTC time: Fri 2017-12-22 12:06:02
Time zone: Europe/Amsterdam (CET, +0100)
NTP enabled: yes
NTP synchronized: yes
RTC in local TZ: no
DST active: no
Last DST change: DST ended at
Sun 2017-10-29 02:59:59 CEST
Sun 2017-10-29 02:00:00 CET
Next DST change: DST begins (the clock jumps one hour forward) at
Sun 2018-03-25 01:59:59 CET
Sun 2018-03-25 03:00:00 CEST
```

## NTP & Chronyd Service

The system can be configured to use a NTP server by setting the option
`timedatectl set-ntp yes`. This will start the chronyd service, if it
wasn’t already started. The current status of the chronyd service can be
obtained with:

``` console
# systemctl status chronyd
● chronyd.service - NTP client/server
Loaded: loaded (/usr/lib/systemd/system/chronyd.service; enabled; vendor preset: enabled)
Active: active (running) since Fri 2017-12-22 10:29:48 IST; 7h ago
Docs: man:chronyd(8)
man:chrony.conf(5)
Main PID: 774 (chronyd)
CGroup: /system.slice/chronyd.service
└─774 /usr/sbin/chronyd

Dec 22 13:06:47 vmPrime.somuVMnet.com chronyd[774]: Source 139.59.43.68 online
Dec 22 13:06:47 vmPrime.somuVMnet.com chronyd[774]: Source 13.126.27.131 online
Dec 22 13:06:47 vmPrime.somuVMnet.com chronyd[774]: Source 139.59.21.22 online
```

The location of the chronyd configuration files can be found by first
finding out which package it comes from, and then performing a series of
rpm queries. The `rpm -qf <file>` command tells us which package
contains that service (i.e., which package installed that file) while
`rpm -qc <package>` shows us the location of all the configuration files
for that package. The method to determine the location of chrony’s
config files is:

``` console
# systemctl status chronyd
● chronyd.service - NTP client/server
Loaded: loaded (/usr/lib/systemd/system/chronyd.service; enabled; vendor preset: enabled)
Active: active (running) since Fri 2017-12-22 10:29:48 IST; 7h ago
Docs: man:chronyd(8)
man:chrony.conf(5)
Main PID: 774 (chronyd)
CGroup: /system.slice/chronyd.service
└─774 /usr/sbin/chronyd

Dec 22 13:06:47 vmPrime.somuVMnet.com chronyd[774]: Source 139.59.43.68 online
Dec 22 13:06:47 vmPrime.somuVMnet.com chronyd[774]: Source 13.126.27.131 online
...
# rpm -qf /usr/lib/systemd/system/chronyd.service
chrony-3.1-2.el7.centos.x86_64
# rpm -qc chrony-3.1-2.el7.centos.x86_64
/etc/chrony.conf
/etc/chrony.keys
/etc/logrotate.d/chrony
/etc/sysconfig/chronyd
```

Thus the chronyd service can be managed by editing the config file
`/etc/chrony.conf`.
