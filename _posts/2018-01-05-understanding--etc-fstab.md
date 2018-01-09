---
layout : post
title : 'Understanding /etc/fstab'
tags : [RHCSA, RHCSA_mod, Managing Partitions, Understanding /etc/fstab]
categories: [RHCSA]
---


The names of the devices in `/etc/fstab` are not based on their actual
device names, but either the LVM volume names or their UUID. The typical
`/etc/fstab` file looks like:

``` bash
# cat fstab

#
# /etc/fstab
# Created by anaconda on Sat Nov 25 08:44:04 2017
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=1c55e935-8099-49c4-8c72-0bc1ff7c396a /boot                   xfs     defaults        0 0
/dev/mapper/centos-home /home                   xfs     defaults        0 0
/dev/mapper/centos-var  /var                    xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
```

The first parameter is the UUID or the LVM volume name. The second is
the directory in the FHS where the disk will be mounted. This is
followed by the file system type and then the mount options. The first
among the last two numbers is the option for backup support,
specifically an old utility called dump-backup. Some enterprise backup
utilities need dump functionality provided by this option to operate,
even though they don’t really use the dump-backup program. The last
number is `fsck` - file system check. The concept is to check the file
system at boot time before it is mounted and data on it is accessed.
There are three valid options: **0** (off), **1** (check the root
\[`/`\] file system) and finally **2** (check non-root file system).
