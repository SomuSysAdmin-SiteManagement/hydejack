---
layout : post
title : 'Understanding LVM Setup'
tags : [RHCSA, RHCSA_mod, Managing LVM Logical Volumes, Understanding LVM Setup]
categories: [RHCSA]
---


When working with LVM, we always start with physical storage media such
as a hard disk (*sda*). Typically, a partition on the hard disk is
marked as the physical volume. Now, this physical volume is added to the
**volume group** which is essentially an abstraction of all the storage
available. Thus, all logical volumes are created from this volume group,
and from their perspective, the physical volume that’s acting as it’s
storage media isn’t important.

Once the logical volume is made from the storage in the volume group, we
get a device called `/dev/vgroup/logvol`. This is the device for the
logical volume on which we create the file system. As long as there is
space on the volume group, we can add new logical volumes on it. If
there isn’t we can also add a physical volume to the volume group, to
increase its capacity.

![LVM
Setup<span label="fig:2 LVM Setup"></span>](RHCSA/Mod2/chapters/2.16.a)
