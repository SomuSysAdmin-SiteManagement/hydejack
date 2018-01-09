---
layout : post
title : 'Understanding the Modular Structure of the Kernel'
tags : [RHCSA, RHCSA_mod, Managing the Kernel, Understanding the Modular Structure of the Kernel]
categories: [RHCSA]
---


The primary responsibility of the Linux Kernel is addressing the
hardware and managing it. By default the kernel contains every
functionality required to address the available hardware.

![Modular structure of the
Kernel<span label="fig:3"></span>](RHCSA/Mod3/chapters/3.17.a)

When a new hardware is added, the **udev** process is awoken to
initialize the new hardware by communicating with the kernel and
providing it with all the necessary information about the device. In
turn, the kernel will load the module specific to that new hardware and
initialize it so that the user can start using it.
