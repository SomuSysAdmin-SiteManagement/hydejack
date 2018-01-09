---
layout : post
title : 'Using virt-manager'
tags : [RHCSA, RHCSA_mod, Working with Virtual Machines, Using virsh]
categories: [RHCSA]
---


The `virt-manager` is a GUI interface for the control of VMs. It can be
started by:

``` console
# virt-manager
```

Note that to control VMs made using `virt-manager` from within `virsh`,
we need to be logged in under the same user when starting these
utilities. They use the same libvirt database of VMs, but the user
context can cause the VMs to be unavailable to some users\!
