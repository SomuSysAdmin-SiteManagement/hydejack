---
layout : post
title : 'Understanding LVM resize operations'
tags : [RHCSA, RHCSA_mod, Managing LVM Logical Volumes, Understanding LVM resize operations]
categories: [RHCSA]
---


The structure of LVMs are simple: the file system (FS) are installed on
Logical Volumes (LV). These Logical Volumes get their disk space from
Volume Groups (VG) which use several Physical Volumes (PV) that actually
hold the data and provides the disk space.

## Extending the File System

To expand the disk capacity of the file system, we need more space in
the Logical volume. This means that (possibly) more space has to be
added to the Volume Group itself, and thus, more physical volumes may
need to be added.

So, first we need to create a new physical volume, and then assign it to
the volume group. Then it is possible to grow the logical volume, and
finally extend the file system.

## Shrinking the File System

At first we have to reduce the size of the file system, and then reduce
the size of the logical volume. If we don’t there will be a file system
with a bigger size than the logical partition it’s residing on.

Thus, after reducing the file system size and then the logical volume
size, we can then reduce the size of the volume group (if needed).
