---
layout : post
title : 'Managing Services in a systemd Environment'
tags : [RHCSA, RHCSA_mod, Managing and Understanding the Boot Procedure, Managing Services in a systemd Environment]
categories: [RHCSA]
---


To get a task done in Linux, we need services, which are started using
systemd. The directory `/usr/lib/systemd/system` contains many service
scripts (among other files). This directory is for the default services
that are installed by the RPMs. Thus, we shouldn’t edit the files in
this directory.

For our own system service management needs, we go to the
`/etc/systemd/system` folder. This has two advantages: i) updates to the
RPMs that dropped the service scripts in `/usr/lib/systemd/system` won’t
overwrite our scripts, and ii) Our scripts in `/etc/systemd/system` will
overwrite those in the other folder.

## Service files

The services form the basic unit of management in systemd is a service.
The service files contain all the information required to start a
service.

Let us consider the `/usr/lib/systemd/system/httpd.service` file:

``` bash
[Unit]
Description=The Apache HTTP Server
After=network.target remote-fs.target nss-lookup.target
Documentation=man:httpd(8)
Documentation=man:apachectl(8)

[Service]
Type=notify
EnvironmentFile=/etc/sysconfig/httpd
ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND
ExecReload=/usr/sbin/httpd $OPTIONS -k graceful
ExecStop=/bin/kill -WINCH ${MAINPID}
# We want systemd to give httpd some time to finish gracefully, but still want
# it to kill httpd after TimeoutStopSec if something went wrong during the
# graceful stop. Normally, Systemd sends SIGTERM signal right after the
# ExecStop, which would kill httpd. We are sending useless SIGCONT here to give
# httpd time to finish.
KillSignal=SIGCONT
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

Due to the usage of systemd, a service in RHEL 7 is much more powerful
than a service in previous versions. It is possible to basically turn
everything into a service and control it using systemd.

The `[Install]` section of the file defines how the service should be
started. The `WantedBy` parameter is defined to set this. Here, the
service must be started by a *target*. Services are assigned to targets
and the targets take care of starting up the services.

Next, we take a look at the service definition. In earlier versions of
RHEL, this section was implemented by the help of large shell scripts.
Now, only a few lines of configuration settings are needed. This section
defines what should be started and how.

## systemctl

Services are managed using the `systemctl` command. For example, to see
the status of a service, we write:

``` console
# systemctl status httpd -l
● httpd.service - The Apache HTTP Server
Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
Active: active (running) since Sat 2017-12-16 09:31:03 IST; 3s ago
Docs: man:httpd(8)
man:apachectl(8)
Main PID: 5831 (httpd)
Status: "Processing requests..."
CGroup: /system.slice/httpd.service
├─5831 /usr/sbin/httpd -DFOREGROUND
├─5840 /usr/sbin/httpd -DFOREGROUND
├─5842 /usr/sbin/httpd -DFOREGROUND
├─5843 /usr/sbin/httpd -DFOREGROUND
├─5844 /usr/sbin/httpd -DFOREGROUND
└─5845 /usr/sbin/httpd -DFOREGROUND

Dec 16 09:31:01 vmPrime.somuVMnet.local systemd[1]: Starting The Apache HTTP Server...
Dec 16 09:31:03 vmPrime.somuVMnet.local systemd[1]: Started The Apache HTTP Server.
```

To start a service we use:

``` console
# systemctl start httpd
```

To stop it we use:

``` console
# systemctl stop httpd
```

To permanently remove the service from the startup procedure of our OS,
we use:

``` console
# systemctl disable httpd
Removed symlink /etc/systemd/system/multi-user.target.wants/httpd.service.
```

To enable the service again:

``` console
# systemctl enable httpd
Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.
```

## Targets

Our systems can enter different states called **targets**, which are
also defined in `/usr/lib/systemd/system` and `/etc/systemd/system`.
Targets act as a collection of services, and we can specify dependency
relations within the target file. Two of the most important targets are:
`multi-user.target` and `graphical.target`, both present in
`/usr/lib/systemd/system` directory.

The *graphical.target* is started as the default environment when a GUI
is running. Contrastingly, the *multi-user.target* is used as a default
environment on servers where a GUI isn’t present.

## Wants

In order to put services in a specific target, we create a *wants*
directory for that target and put a symbolic link to that service in
that directory. Services belong to a specific target. When a service is
enabled, a symbolic link is created in some *Wants* directory. Each
target has its own wants directory that ends with the name of the target
followed by a `.wants`. For example, the `multi-user.target` has a
corresponding directory called `multi-user.target.wants` in the same
folder.

These directories only contain symbolic links to services that should be
available at all times in that particular target. For example, the
`multi-user.target.wants` contains:

``` console
# ls -l /usr/lib/systemd/system/multi-user.target.wants/
total 0
lrwxrwxrwx. 1 root root 16 Nov 25 08:50 brandbot.path -> ../brandbot.path
lrwxrwxrwx. 1 root root 15 Nov 25 08:50 dbus.service -> ../dbus.service
lrwxrwxrwx. 1 root root 15 Nov 25 10:14 getty.target -> ../getty.target
lrwxrwxrwx. 1 root root 24 Nov 25 08:50 plymouth-quit.service -> ../plymouth-quit.service
lrwxrwxrwx. 1 root root 29 Nov 25 08:50 plymouth-quit-wait.service -> ../plymouth-quit-wait.service
lrwxrwxrwx. 1 root root 33 Nov 25 10:14 systemd-ask-password-wall.path -> ../systemd-ask-password-wall.path
lrwxrwxrwx. 1 root root 25 Nov 25 10:14 systemd-logind.service -> ../systemd-logind.service
lrwxrwxrwx. 1 root root 39 Nov 25 10:14 systemd-update-utmp-runlevel.service -> ../systemd-update-utmp-runlevel.service
lrwxrwxrwx. 1 root root 32 Nov 25 10:14 systemd-user-sessions.service -> ../systemd-user-sessions.service
```

Further, there are also the services resident in
`/etc/systemd/system/multi-user.target.wants` which will also be
included:

``` console
# ls -l /etc/systemd/system/multi-user.target.wants/
total 0
lrwxrwxrwx. 1 root root 41 Nov 25 08:51 abrt-ccpp.service -> /usr/lib/systemd/system/abrt-ccpp.service
lrwxrwxrwx. 1 root root 37 Nov 25 08:50 abrtd.service -> /usr/lib/systemd/system/abrtd.service
lrwxrwxrwx. 1 root root 41 Nov 25 08:50 abrt-oops.service -> /usr/lib/systemd/system/abrt-oops.service
lrwxrwxrwx. 1 root root 43 Nov 25 08:51 abrt-vmcore.service -> /usr/lib/systemd/system/abrt-vmcore.service
lrwxrwxrwx. 1 root root 41 Nov 25 08:50 abrt-xorg.service -> /usr/lib/systemd/system/abrt-xorg.service
lrwxrwxrwx. 1 root root 35 Nov 25 08:59 atd.service -> /usr/lib/systemd/system/atd.service
lrwxrwxrwx. 1 root root 38 Nov 25 08:51 auditd.service -> /usr/lib/systemd/system/auditd.service
lrwxrwxrwx. 1 root root 44 Nov 25 08:59 avahi-daemon.service -> /usr/lib/systemd/system/avahi-daemon.service
lrwxrwxrwx. 1 root root 39 Nov 25 08:51 chronyd.service -> /usr/lib/systemd/system/chronyd.service
lrwxrwxrwx. 1 root root 37 Nov 25 08:50 crond.service -> /usr/lib/systemd/system/crond.service
lrwxrwxrwx. 1 root root 33 Nov 25 08:55 cups.path -> /usr/lib/systemd/system/cups.path
lrwxrwxrwx. 1 root root 36 Nov 25 08:55 cups.service -> /usr/lib/systemd/system/cups.service
lrwxrwxrwx. 1 root root 41 Nov 25 08:51 firewalld.service -> /usr/lib/systemd/system/firewalld.service
lrwxrwxrwx. 1 root root 37 Dec 16 11:32 httpd.service -> /usr/lib/systemd/system/httpd.service
lrwxrwxrwx. 1 root root 42 Nov 25 08:59 irqbalance.service -> /usr/lib/systemd/system/irqbalance.service
lrwxrwxrwx. 1 root root 37 Nov 25 08:51 kdump.service -> /usr/lib/systemd/system/kdump.service
lrwxrwxrwx. 1 root root 35 Nov 25 08:51 ksm.service -> /usr/lib/systemd/system/ksm.service
lrwxrwxrwx. 1 root root 40 Nov 25 08:51 ksmtuned.service -> /usr/lib/systemd/system/ksmtuned.service
lrwxrwxrwx. 1 root root 46 Nov 25 08:50 libstoragemgmt.service -> /usr/lib/systemd/system/libstoragemgmt.service
lrwxrwxrwx. 1 root root 40 Nov 25 08:52 libvirtd.service -> /usr/lib/systemd/system/libvirtd.service
lrwxrwxrwx. 1 root root 38 Nov 25 08:59 mcelog.service -> /usr/lib/systemd/system/mcelog.service
lrwxrwxrwx. 1 root root 41 Nov 25 08:51 mdmonitor.service -> /usr/lib/systemd/system/mdmonitor.service
lrwxrwxrwx. 1 root root 44 Nov 25 08:59 ModemManager.service -> /usr/lib/systemd/system/ModemManager.service
lrwxrwxrwx. 1 root root 46 Nov 25 08:50 NetworkManager.service -> /usr/lib/systemd/system/NetworkManager.service
lrwxrwxrwx. 1 root root 41 Nov 25 08:52 nfs-client.target -> /usr/lib/systemd/system/nfs-client.target
lrwxrwxrwx. 1 root root 39 Nov 25 08:59 postfix.service -> /usr/lib/systemd/system/postfix.service
lrwxrwxrwx. 1 root root 40 Nov 25 08:50 remote-fs.target -> /usr/lib/systemd/system/remote-fs.target
lrwxrwxrwx. 1 root root 36 Nov 25 08:59 rngd.service -> /usr/lib/systemd/system/rngd.service
lrwxrwxrwx. 1 root root 39 Nov 25 08:59 rsyslog.service -> /usr/lib/systemd/system/rsyslog.service
lrwxrwxrwx. 1 root root 38 Nov 25 08:59 smartd.service -> /usr/lib/systemd/system/smartd.service
lrwxrwxrwx. 1 root root 36 Nov 25 08:59 sshd.service -> /usr/lib/systemd/system/sshd.service
lrwxrwxrwx. 1 root root 39 Nov 25 08:59 sysstat.service -> /usr/lib/systemd/system/sysstat.service
lrwxrwxrwx. 1 root root 37 Nov 25 08:59 tuned.service -> /usr/lib/systemd/system/tuned.service
lrwxrwxrwx. 1 root root 40 Nov 25 08:51 vmtoolsd.service -> /usr/lib/systemd/system/vmtoolsd.service
```

Now, the `/etc/systemd/system/default.target` defines which target
(graphical or multi-user) is set as the default environment post-boot
for the users.

``` console
# ls -l /etc/systemd/system/default.target
lrwxrwxrwx. 1 root root 36 Nov 25 09:08 /etc/systemd/system/default.target -> /lib/systemd/system/graphical.target
```

Above, we can see that the graphical.target is set as the default. If we
want to change that, we just change the link to point to
`/lib/systemd/system/multi-user.target` to operate in a CLI by default.

## Viewing Currently Loaded Targets

To view the currently loaded targets we use:

``` console
# systemctl list-units --type=target
UNIT                   LOAD   ACTIVE SUB    DESCRIPTION
basic.target           loaded active active Basic System
bluetooth.target       loaded active active Bluetooth
cryptsetup.target      loaded active active Encrypted Volumes
getty.target           loaded active active Login Prompts
graphical.target       loaded active active Graphical Interface
local-fs-pre.target    loaded active active Local File Systems (Pre)
local-fs.target        loaded active active Local File Systems
multi-user.target      loaded active active Multi-User System
network-online.target  loaded active active Network is Online
network-pre.target     loaded active active Network (Pre)
network.target         loaded active active Network
nfs-client.target      loaded active active NFS client services
nss-user-lookup.target loaded active active User and Group Name Lookups
paths.target           loaded active active Paths
remote-fs-pre.target   loaded active active Remote File Systems (Pre)
remote-fs.target       loaded active active Remote File Systems
slices.target          loaded active active Slices
sockets.target         loaded active active Sockets
sound.target           loaded active active Sound Card
swap.target            loaded active active Swap
sysinit.target         loaded active active System Initialization
timers.target          loaded active active Timers

LOAD   = Reflects whether the unit definition was properly loaded.
ACTIVE = The high-level unit activation state, i.e. generalization of SUB.
SUB    = The low-level unit activation state, values depend on unit type.

22 loaded units listed. Pass --all to see loaded but inactive units, too.
To show all installed unit files use 'systemctl list-unit-files'.
```

The services provided by our entire OS are not packed together into one
monolithic target, but broken down into several targets that
concurrently active, as can be seen above. How these targets are
supposed to work together is also defined in the target files. For
example, in the `/usr/lib/systemd/system/multi-user.target` file:

``` bash
[Unit]
Description=Multi-User System
Documentation=man:systemd.special(7)
Requires=basic.target
Conflicts=rescue.service rescue.target
After=basic.target rescue.service rescue.target
AllowIsolate=yes
```

In this, we can see that the `multi-user.target` requires the
*basic.target* to be loaded. It conflicts with *rescue.target* and it
has to be loaded only after the *basic.target* is loaded.

Thus, when systemd will try to load the *multi-user.target*, it’ll first
check the dependencies of the target, which is *basic.target*. If it’s
not currently loaded, systemd attempts to start the *basic.target* after
resolving all of its dependencies, and so on.
