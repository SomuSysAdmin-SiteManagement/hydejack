---
layout : post
title : 'Mounting the Partition Manually'
tags : [RHCSA, RHCSA_mod, Managing Partitions, Mounting the Partition Manually]
categories: [RHCSA]
---


The new partition is mounted using the `mount` command. For recurring
mounting, it’s advisable to create a permanent mounting directory. For
temporary mounts, we can use `/mnt`. The mounting operation can be
verified by typing the `mount` command. The command to mount the new
partition `sdb1` on the `/mnt` directory is :

``` console
# mount /dev/sdb1 /mnt
# mount | tail -n 5
tmpfs on /run/user/1000 type tmpfs (rw,nosuid,nodev,relatime,seclabel,size=592968k,mode=700,uid=1000,gid=1000)
fusectl on /sys/fs/fuse/connections type fusectl (rw,relatime)
gvfsd-fuse on /run/user/1000/gvfs type fuse.gvfsd-fuse (rw,nosuid,nodev,relatime,user_id=1000,group_id=1000)
/dev/sr0 on /run/media/somu/CentOS 7 x86_64 type iso9660 (ro,nosuid,nodev,relatime,uid=1000,gid=1000,iocharset=utf8,mode=0400,dmode=0500,uhelper=udisks2)
/dev/sdb1 on /mnt type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
```

**Mounting** means connecting some device or functionality to a
particular directory. This not only includes removable media and
peripheral device directories, but also many system settings (such as
the `/proc` file system or the `/sys` file system).

To view only the devices that have been mounted, we can use:

``` console
# mount | grep ^/dev
/dev/mapper/centos-root on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/mapper/centos-home on /home type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sda2 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/mapper/centos-var on /var type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sr0 on /run/media/somu/CentOS 7 x86_64 type iso9660 (ro,nosuid,nodev,relatime,uid=1000,gid=1000,iocharset=utf8,mode=0400,dmode=0500,uhelper=udisks2)
/dev/sdb1 on /mnt type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
```

## umount

The `umount` command is used to unmount a mounted directory. This is to
ensure that no files are open and cannot be damaged by the sudden
removal of the file system. The `umount` command takes as parameter
either the device name or the directory where it is mounted. So, both
`/dev/sdb1` and `/mnt` are valid parameters to unmount the new
partition.

``` console
# umount /dev/sdb1
# mount | grep ^/dev
/dev/mapper/centos-root on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/mapper/centos-home on /home type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sda2 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/mapper/centos-var on /var type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sr0 on /run/media/somu/CentOS 7 x86_64 type iso9660 (ro,nosuid,nodev,relatime,uid=1000,gid=1000,iocharset=utf8,mode=0400,dmode=0500,uhelper=udisks2)
```

The device `/dev/sdb1` is no longer mounted, as can be seen from the
output. A major challenge that may be presented by this is the fact that
the device names may change at any time\! Today the device that’s called
`/dev/sdb1` may change to `/dev/sdc1` if the OS detects the devices (and
names them) in another order, thus making our references to them
useless. For this reason the *Universally Unique ID (UUID)* of a device
can be used to refer to it. The UUID of all existing devices can be
displayed using:

``` console
# blkid
/dev/sda2: UUID="1c55e935-8099-49c4-8c72-0bc1ff7c396a" TYPE="xfs"
/dev/sda3: UUID="DfepDW-igyh-eI6D-SgBB-3HV5-QTQT-EI3Pc2" TYPE="LVM2_member"
/dev/sdb1: LABEL="myfs" UUID="00a8c244-8781-492c-a6ad-85624780e1e8" TYPE="xfs"
/dev/sr0: UUID="2017-09-06-10-53-42-00" LABEL="CentOS 7 x86_64" TYPE="iso9660" PTTYPE="dos"
/dev/mapper/centos-root: UUID="d2fe3168-4eef-431b-9a8e-eb59dae10bcb" TYPE="xfs"
/dev/mapper/centos-swap: UUID="24b0103c-d574-4623-bc85-9255076e3b7d" TYPE="swap"
/dev/mapper/centos-var: UUID="ed13b5f3-1b26-48f7-81cb-03a2bad5fc61" TYPE="xfs"
/dev/mapper/centos-home: UUID="710a33e6-7e52-4c06-830d-e53ae0d58fed" TYPE="xfs"
```

As can be seen, the label for the file system is also shown using the
`blkid` command. Both the UUID and the label for the file system can be
used to reference the device while using the mount command:

``` console
# mount LABEL=myfs /mnt
# mount | tail -n 3
gvfsd-fuse on /run/user/1000/gvfs type fuse.gvfsd-fuse (rw,nosuid,nodev,relatime,user_id=1000,group_id=1000)
/dev/sr0 on /run/media/somu/CentOS 7 x86_64 type iso9660 (ro,nosuid,nodev,relatime,uid=1000,gid=1000,iocharset=utf8,mode=0400,dmode=0500,uhelper=udisks2)
/dev/sdb1 on /mnt type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
```

