---
layout : post
title : 'Mounting partitions via /etc/fstab'
tags : [RHCSA, RHCSA_mod, Managing Partitions, Mounting partitions via /etc/fstab]
categories: [RHCSA]
---


To automount the device `/dev/sdb1`, all we need to do is add the
following line to `/etc/fstab`:

``` bash
/dev/sdb1   /data   xfs     defaults    1   2
```

Note however that the above won’t guarantee that the *proper* file
system will always be mounted as it’s dependent on the order in which
the OS detects the disks\! So, it’s better to use the UUIDs of the file
systems to track them. We use the UUID to auto mount the file system
with:

``` bash
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
UUID=00a8c244-8781-492c-a6ad-85624780e1e8 /data xfs     defaults        1 2
```

To confirm the mount, we use the `mount -a` command, which tries to
mount everything that hasn’t been mounted yet.

``` console
# mount -a
mount: mount point /data does not exist
```

In this case, since the directory `/data` doesn’t exist, the mounting
failed. The mount system doesn’t create a new directory (mounting
location) if it doesn’t yet exist. We remedy this by:

``` console
# ls
bin  boot  dev  downloads  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
# mkdir data
# mount -a
# mount | grep ^/dev/
/dev/mapper/centos-root on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/mapper/centos-home on /home type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sda2 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/mapper/centos-var on /var type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sr0 on /run/media/somu/CentOS 7 x86_64 type iso9660 (ro,nosuid,nodev,relatime,uid=1000,gid=1000,iocharset=utf8,mode=0400,dmode=0500,uhelper=udisks2)
/dev/sdb1 on /mnt type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sdb1 on /data type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
```

As is evident from the last line of the output, the file system has been
properly mounted and is ready for use. An alternative way to mount it
using fstab would’ve been to use the label for the disk instead of the
UUID, such as:

``` bash
LABEL=myfs  /data   xfs defaults    1   2
```

## Managing xfs file systems using xfs\_ commands

The new xfs file system provides a set of commands that start with
`xfx_` that help administer any xfs partition. They are:

``` bash
xfs_admin      xfs_copy       xfs_estimate   xfs_fsr        xfs_info
xfs_logprint   xfs_metadump   xfs_ncheck     xfs_repair     xfs_bmap
xfs_db         xfs_freeze     xfs_growfs     xfs_io         xfs_mdrestore
xfs_mkfile     xfs_quota      xfs_rtcp
```

To add a new label to the boot device, we use `xfs_admin` command with
the `-L` command. Let us say, we want to label the boot partition on our
system. To find out which partition is mapped to `/boot` we use the
`lsblk` command.

``` console
# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
sda1            8:1    0    2M  0 part
sda2            8:2    0  488M  0 part /boot
sda3            8:3    0 14.9G  0 part
centos-root 253:0    0  3.7G  0 lvm  /
centos-swap 253:1    0  1.9G  0 lvm  [SWAP]
centos-var  253:2    0  1.9G  0 lvm  /var
centos-home 253:3    0  7.5G  0 lvm  /home
sdb               8:16   0   10G  0 disk
sdb1            8:17   0    1G  0 part /data
sr0              11:0    1  8.1G  0 rom  /run/media/somu/CentOS 7 x86_64
# blkid
/dev/sda2: UUID="1c55e935-8099-49c4-8c72-0bc1ff7c396a" TYPE="xfs"
/dev/sda3: UUID="DfepDW-igyh-eI6D-SgBB-3HV5-QTQT-EI3Pc2" TYPE="LVM2_member"
/dev/sdb1: LABEL="myfs" UUID="00a8c244-8781-492c-a6ad-85624780e1e8" TYPE="xfs"
/dev/sr0: UUID="2017-09-06-10-53-42-00" LABEL="CentOS 7 x86_64" TYPE="iso9660" PTTYPE="dos"
/dev/mapper/centos-root: UUID="d2fe3168-4eef-431b-9a8e-eb59dae10bcb" TYPE="xfs"
/dev/mapper/centos-swap: UUID="24b0103c-d574-4623-bc85-9255076e3b7d" TYPE="swap"
/dev/mapper/centos-var: UUID="ed13b5f3-1b26-48f7-81cb-03a2bad5fc61" TYPE="xfs"
/dev/mapper/centos-home: UUID="710a33e6-7e52-4c06-830d-e53ae0d58fed" TYPE="xfs"
# xfs_admin -L bootdevice /dev/sda2
xfs_admin: /dev/sda2 contains a mounted filesystem

fatal error -- couldn't initialize XFS library
```

The `/boot` partition was auto-mounted at start up, and thus it needs to
be unmounted first before it can be edited. So, we take the following
steps:

``` console
# umount /boot
# xfs_admin -L bootdevice /dev/sda2
writing all SBs
new label = "bootdevice"
# mount -a
# blkid
/dev/sda2: LABEL="bootdevice" UUID="1c55e935-8099-49c4-8c72-0bc1ff7c396a" TYPE="xfs"
/dev/sda3: UUID="DfepDW-igyh-eI6D-SgBB-3HV5-QTQT-EI3Pc2" TYPE="LVM2_member"
/dev/sdb1: LABEL="myfs" UUID="00a8c244-8781-492c-a6ad-85624780e1e8" TYPE="xfs"
/dev/sr0: UUID="2017-09-06-10-53-42-00" LABEL="CentOS 7 x86_64" TYPE="iso9660" PTTYPE="dos"
/dev/mapper/centos-root: UUID="d2fe3168-4eef-431b-9a8e-eb59dae10bcb" TYPE="xfs"
/dev/mapper/centos-swap: UUID="24b0103c-d574-4623-bc85-9255076e3b7d" TYPE="swap"
/dev/mapper/centos-var: UUID="ed13b5f3-1b26-48f7-81cb-03a2bad5fc61" TYPE="xfs"
/dev/mapper/centos-home: UUID="710a33e6-7e52-4c06-830d-e53ae0d58fed" TYPE="xfs"
```

