---
layout : post
title : 'Creating an LVM Logical Volume'
tags : [RHCSA, RHCSA_mod, Managing LVM Logical Volumes, Creating an LVM Logical Volume]
categories: [RHCSA]
---


To create a new LVM partition, first we need to make a partition like
any other partition.

``` console
# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p

Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xf11ab429

Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     4196351     2097152    5  Extended
/dev/sdb5            4096     2101247     1048576   83  Linux
/dev/sdb6         2103296     4196351     1046528   83  Linux

Command (m for help): n
Partition type:
p   primary (0 primary, 1 extended, 3 free)
l   logical (numbered from 5)
Select (default p): p
Partition number (2-4, default 2):
First sector (4196352-20971519, default 4196352):
Using default value 4196352
Last sector, +sectors or +size{K,M,G} (4196352-20971519, default 20971519): +100M
Partition 2 of type Linux and of size 100 MiB is set

Command (m for help): p

Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xf11ab429

Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     4196351     2097152    5  Extended
/dev/sdb2         4196352     4401151      102400   83  Linux
/dev/sdb5            4096     2101247     1048576   83  Linux
/dev/sdb6         2103296     4196351     1046528   83  Linux
# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p

Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xf11ab429

Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     4196351     2097152    5  Extended
/dev/sdb5            4096     2101247     1048576   83  Linux
/dev/sdb6         2103296     4196351     1046528   83  Linux

Command (m for help): n
Partition type:
p   primary (0 primary, 1 extended, 3 free)
l   logical (numbered from 5)
Select (default p): p
Partition number (2-4, default 2):
First sector (4196352-20971519, default 4196352):
Using default value 4196352
Last sector, +sectors or +size{K,M,G} (4196352-20971519, default 20971519): +100M
Partition 2 of type Linux and of size 100 MiB is set

Command (m for help): p

Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xf11ab429

Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     4196351     2097152    5  Extended
/dev/sdb2         4196352     4401151      102400   83  Linux
/dev/sdb5            4096     2101247     1048576   83  Linux
/dev/sdb6         2103296     4196351     1046528   83  Linux
```

Now that we have specified the details of our new partition, we need to
change one more before we can use the LVM partitions. We enter the
command `t` to change the partition type, and then show the overview of
all the acceptable partition types using `l`. From the command below, we
can see that there is a partition type called **Linux LVM** which suits
our requirements.

``` console
Command (m for help): t
Partition number (1,2,5,6, default 6): 2
Hex code (type L to list all codes): L

0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris
1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden C:  c6  DRDOS/sec (FAT-
4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx
5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data
6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility
8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt
9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access
a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O
b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor
c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi eb  BeOS fs
e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         ee  GPT
f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ef  EFI (FAT-12/16/
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        f0  Linux/PA-RISC b
11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f1  SpeedStor
12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f4  SpeedStor
14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f2  DOS secondary
16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      fb  VMware VMFS
17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fc  VMware VMKCORE
18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fd  Linux raid auto
1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fe  LANstep
1c  Hidden W95 FAT3 75  PC/IX           be  Solaris boot    ff  BBT
1e  Hidden W95 FAT1 80  Old Minix
Hex code (type L to list all codes): 8e
Changed type of partition 'Linux' to 'Linux LVM'

Command (m for help): p

Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xf11ab429

Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     4196351     2097152    5  Extended
/dev/sdb2         4196352     4401151      102400   8e  Linux LVM
/dev/sdb5            4096     2101247     1048576   83  Linux
/dev/sdb6         2103296     4196351     1046528   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
# partprobe
```

We enter the value `8e` since it’s the code for the Linux LVM partition
that we need. Finally, we use `p` to print the partition table and
verify our partition, `w` to save the changes and `partprobe` to push
the changes to the kernel.

## Creating a Physical Volume

A physical volume is just a partition with the LVM metadata added to it.
The volume groups built from the PVs are not possible to build without
this metadata stored in the partitions. The physical volumes are created
using `pvcreate`. We can show all physical volumes using `pvs`.

``` console
# pvcreate /dev/sdb2
Physical volume "/dev/sdb2" successfully created.
# pvs
PV         VG     Fmt  Attr PSize   PFree
/dev/sda3  centos lvm2 a--  <14.91g   4.00m
/dev/sdb2         lvm2 ---  100.00m 100.00m
```

The `pvs` command tells us that we have a physical volume called
`/dev/sdb2` which isn’t in a volume group yet, has a LVM2 formatting,
has a partition size of 100MB and has the same amount of free space.

## Creating a Volume Group

Next, we create a new volume group using the `vgcreate` command. Again,
we can check the volume groups on our system using the `vgs` command.

``` console
# vgcreate vgPrime /dev/sdb2
Volume group "vgPrime" successfully created
# vgs
VG      #PV #LV #SN Attr   VSize   VFree
centos    1   4   0 wz--n- <14.91g  4.00m
vgPrime   1   0   0 wz--n-  96.00m 96.00m
```

The volume group *vgPrime* has 1 PV in it (*/dev/sda2*), no logical
volumes, And has a Volume size of 96MB, all of which is free\!

## Creating a Logical Volume

The creating of a Logical Volume on a VG requires specifying the size of
the logical volume. This can be done in two ways: by counting the number
of extents (building blocks of LVM) \[`-l`\] or the actual size on disk
(KB, MB, GB, TB, etc.)\[`-L`\]. Finally, we also can provide the name of
the LV using the `-n` option.

``` console
# lvcreate -n lvPrime -L 96M vgPrime
Logical volume "lvPrime" created.
# lvs
LV      VG      Attr       LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
home    centos  -wi-ao----  7.45g
root    centos  -wi-ao----  3.72g
swap    centos  -wi-ao----  1.86g
var     centos  -wi-ao----  1.86g
lvPrime vgPrime -wi-a----- 96.00m
```

## Creating a File system on the LV

Now, since the LV is ready, we can put a file system on it. We refer to
the logical volume device by
`/dev/<volumeGroupName>/<logicalVolumeName>`.

``` console
# mkfs.ext2 /dev/vgPrime/lvPrime
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=1024 (log=0)
Fragment size=1024 (log=0)
Stride=0 blocks, Stripe width=0 blocks
24576 inodes, 98304 blocks
4915 blocks (5.00%) reserved for the super user
First data block=1
Maximum filesystem blocks=67371008
12 block groups
8192 blocks per group, 8192 fragments per group
2048 inodes per group
Superblock backups stored on blocks:
8193, 24577, 40961, 57345, 73729

Allocating group tables: done
Writing inode tables: done
Writing superblocks and filesystem accounting information: done

# mount /dev/vgPrime/lvPrime /mnt
# mount | grep ^/dev/
/dev/mapper/centos-root on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sdb5 on /data type ext4 (rw,relatime,seclabel,data=ordered)
/dev/mapper/centos-var on /var type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/mapper/centos-home on /home type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sda2 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sr0 on /run/media/somu/CentOS 7 x86_64 type iso9660 (ro,nosuid,nodev,relatime,uid=1000,gid=1000,iocharset=utf8,mode=0400,dmode=0500,uhelper=udisks2)
/dev/mapper/vgPrime-lvPrime on /mnt type ext2 (rw,relatime,seclabel)
```

Here, we can see that there is a strange behavior with the device that
is mounted. While we issued the command to mount `/dev/vgPrime/lvPrime`,
the device that was actually mounted is shown as
`/dev/mapper/vgPrime-lvPrime`. This is because both those names are
symlinks to the real name of the device (*dm-5*):

``` console
# ls -l /dev/mapper/vgPrime-lvPrime /dev/vgPrime/lvPrime
lrwxrwxrwx. 1 root root 7 Dec 11 15:13 /dev/mapper/vgPrime-lvPrime -> ../dm-5
lrwxrwxrwx. 1 root root 7 Dec 11 15:13 /dev/vgPrime/lvPrime -> ../dm-5
```

The device `/dev/dm-5` is a Device Mapper device, which is the same as
used in case of LUKS encrypted volumes.
