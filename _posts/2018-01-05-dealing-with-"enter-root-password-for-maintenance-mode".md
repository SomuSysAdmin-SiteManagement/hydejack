---
layout : post
title : 'Dealing with "Enter root password for maintenance mode"'
tags : [RHCSA, RHCSA_mod, Managing Partitions, Creating a LUKS Encrypted Partition]
categories: [RHCSA]
---


If there is an error within our `/etc/fstab` file, our OS will fail to
boot. This puts us in emergency mode, which lets us log in as the root
user to troubleshoot. Since it’s a boot-time error, a good idea is to
use the `journalctl -xb` to view the `journald` logs, which may help us
locate the problem.

If the error is along the line of "the device timed out", then there is
probably a typo in the device name. We can then fix the error and reboot
the server.
