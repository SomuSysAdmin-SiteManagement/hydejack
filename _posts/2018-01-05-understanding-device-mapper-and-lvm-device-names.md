---
layout : post
title : 'Understanding Device Mapper and LVM Device Names'
tags : [RHCSA, RHCSA_mod, Managing LVM Logical Volumes, Understanding Device Mapper and LVM Device Names]
categories: [RHCSA]
---


The device mapper is an abstraction layer that the kernel works with to
communicate with certain types of storage devices. Both LUKS encrypted
partitions and LVM use the device mapper. Other devices such as software
RAID and multipath also have to communicate via the device mapper.

Contrastingly, the XFS, Ext4, etc file systems work with the help of the
VFS (Virtual File System) layer (instead of the Device Mapper
abstraction layer). The device mapper has the devices present as `dm-*`
(*dm-0, dm-1, etc.*) but we shouldn’t use them. The names are assigned
during boot, and are subject to change at any time\! This is why the
device mapper provides a bunch of symlinks to the related devices in the
`/device/mapper` directory. They are: `/dev/mapper/vg-lv` and
`/dev/vg/lv` which are both symlinks to the same device.
