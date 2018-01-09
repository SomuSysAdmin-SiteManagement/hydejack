---
layout : post
title : 'Understanding Systemd'
tags : [RHCSA, RHCSA_mod, Managing and Understanding the Boot Procedure, Understanding Systemd]
categories: [RHCSA]
---


**Systemd** is a major new feature added in RHEL 7. It is a new init
system that starts things - it both bootstraps the current user-space as
well as manage the system processes after booting.

During startup, right after the loading of the kernel, systemd is
started, and systemd in turn takes care of starting everything else.
Unlike the older *runlevel* system, where only services were started,
systemd takes care of services, mounting partitions, auto mounting file
systems, and much more.

## Unit file

A **unit** file is the replacement of the old init script. Init scripts
were relatively more difficult to understand. The unit files have
greater readability.

This unit file defines how to start services and everything else systemd
can do, as well as define the relation between all those things. Systemd
has two different locations for the storing of scripts - the default
scripts are stored in `/usr/lib/systemd` and the administrator’s custom
scripts reside in the `/etc/systemd` directory.
