---
layout : post
title : 'Understanding systemd Targets'
tags : [RHCSA, RHCSA_mod, Managing and Understanding the Boot Procedure, Understanding systemd Targets]
categories: [RHCSA]
---


Unit files are everything that can be started by systemd. A category of
unit files are *targets*. Systemd targets are a collection of unit files
that are meant to work together to let the system enter a specific
state. Some of these targets are the equivalent of runlevels in the
previous versions of RHEL. For example:

<span>rM<span>0.73</span></span> **Options** &**Description**  
**poweroff.target** \&State that shuts down the computer.  
**rescue.target** \&Lets the system enter a troubleshooting mode.  
**multi-user.target** \&Fully operational server with a command line,
but without a GUI.  
**graphical.target** \&Fully operational server with a GUI.  
**reboot.target** \&State that causes the computer to reboot.  
**emergency.target** \&Minimalistic rescue mode, to be used when rescue
mode fails.  

## Services related to targets

The services need to know which target they belong to, and the targets
themselves need to know about the ordering. By the use of *wants*, every
service knows by which target it is wanted. For example, every service
has an Install section containing the name of the target that wants it.
The `sshd.service` has:

``` bash
[Install]
WantedBy=multi-user.target
```

### Ordering

The order between targets is defined in targets. For example, the
*multi-user.target* file contains:

``` bash
[Unit]
Description=Multi-User System
Documentation=man:systemd.special(7)
Requires=basic.target
Conflicts=rescue.service rescue.target
After=basic.target rescue.service rescue.target
AllowIsolate=yes
```

Here, we see that the target (and consequently, the services in it) can
only be loaded if the basic.target is already loaded (since it’s
required). Further, systemd may only attempt to start the services in
this target *after* the basic.target has been loaded, and the conflicted
*rescue.target* was ordered to load (but didn’t). The `AllowIsolate=yes`
signifies whether the system can jump from another target to this target
to change it’s state.
