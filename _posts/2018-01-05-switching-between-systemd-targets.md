---
layout : post
title : 'Switching between systemd Targets'
tags : [RHCSA, RHCSA_mod, Managing and Understanding the Boot Procedure, Switching between systemd Targets]
categories: [RHCSA]
---


While changing from one system state to another, only certain targets
may switch to another one from an operational environment, but in may
cases, we can’t. For example, it is possible to go from an operational
environment to a minimal environment such as the rescue mode.

However, any target can be booted to from the Grub boot menu. The
currently active targets can be listed with:

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

## Switching to another target from an operational environment

Working environments consist of multiple targets, some of which are
listed above. To change to another target (mode), we use the `systemctl
isolate` command:

``` console
# systemctl isolate rescue.target
Give root password for maintenance
(or type Control-D to continue):
```

To exit rescue mode, we must just type `exit` and let the computer
reboot, since it’s not possible to easily switch from the rescue mode to
any other mode.

## Selecting target from Grub Boot menu

When the grub boot menu is displayed, and the available kernels are
shown, we can press the `e` key to enter boot options. In here, we have
to go down to the line that starts with `linux16` and at the very end,
we type: `systemd.unit=<targetName>.target` to boot into it. For
example, to boot into the rescue mode during boot, we use:

``` bash
systemd.unit=rescue.target
```

Then, we have to press *CTRL+X* to execute. This will directly boot us
into the rescue mode. In this mode, the `systemctl list-units
--type=target` returns only a few targets, which proves that this mode
is indeed minimalistic, but also the loaded targets (i.e., the services
loaded by them) are essential for proper functioning of the computer.

## Emergency mode

To boot into the emergency mode we need to use
`systemctl.unit=emergency.target`. In this mode, `systemctl list-units
--type=targets` doesn’t return anything. We can use `systemctl default`
to start the default target.
