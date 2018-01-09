---
layout : post
title : 'Growing an LVM Logical Volume'
tags : [RHCSA, RHCSA_mod, Managing LVM Logical Volumes, Growing an LVM Logical Volume]
categories: [RHCSA]
---


We can grow the LVM volume if we’re running out of disk space and want
to make it bigger. We typically check the amount of free space using `df
-h` (*disk free - human-readable*) command:

``` console
# df -h
Filesystem                   Size  Used Avail Use% Mounted on
/dev/mapper/centos-root      3.8G  3.6G  163M  96% /
devtmpfs                     2.9G     0  2.9G   0% /dev
tmpfs                        2.9G     0  2.9G   0% /dev/shm
tmpfs                        2.9G  9.1M  2.9G   1% /run
tmpfs                        2.9G     0  2.9G   0% /sys/fs/cgroup
/dev/sdb5                    976M  2.6M  907M   1% /data
/dev/mapper/centos-var       1.9G  365M  1.5G  20% /var
/dev/mapper/centos-home      7.5G   68M  7.4G   1% /home
/dev/sda2                    485M  266M  220M  55% /boot
tmpfs                        580M  4.0K  580M   1% /run/user/42
tmpfs                        580M   36K  580M   1% /run/user/1000
/dev/sr0                     8.1G  8.1G     0 100% /run/media/somu/CentOS 7 x86_64
/dev/mapper/vgPrime-lvPrime   93M  1.6M   87M   2% /mnt
```

Now, since it’s an LVM, the order in which we grow the different
components matter. To make the filesystem bigger, first we check the LV
size and then check to see if there’s any free space in the VG:

``` console
# lvs
LV    VG               Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
root  centos_cliserver -wi-ao---- <17.00g
swap  centos_cliserver -wi-ao----   2.00g
lvCLI vgCLI            -wi-a----- 100.00m
# vgs
VG               #PV #LV #SN Attr   VSize   VFree
centos_cliserver   1   2   0 wz--n- <19.00g      0
vgCLI              2   1   0 wz--n- 192.00m  92.00m
```

From the last line of the command, we can see that vgPrime has 0 VFree
(i.e., free space in the VG). Thus, we need to work bottom up and make
it bigger before we can make the LV and the FS bigger. So, we run
`fdisk` on */dev/sdb*.

``` console
# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p

Disk /dev/sdb: 4294 MB, 4294967296 bytes, 8388608 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x9287c46d

Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048      206847      102400   83  Linux
/dev/sdb2          206848      411647      102400   83  Linux
/dev/sdb3          411648      821247      204800   83  Linux LVM
```

## Creating a new logical volume in an extended partition to add to the VG

Now, we add a new partition. However, since on the given disk there’s
already 3 primary partitions, and there can only be a total of 4
partitions on a disk (max of 3 primary and 1 extended that can contain
several logical partitions), the system defaults the last partition to
be an extended one.

``` console
Command (m for help): n
Partition type:
p   primary (3 primary, 0 extended, 1 free)
e   extended
Select (default e):
Using default response e
Selected partition 4
First sector (821248-8388607, default 821248):
Using default value 821248
Last sector, +sectors or +size{K,M,G} (821248-8388607, default 8388607):
Using default value 8388607
Partition 4 of type Extended and of size 3.6 GiB is set

Command (m for help): p

Disk /dev/sdb: 4294 MB, 4294967296 bytes, 8388608 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x9287c46d

Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048      206847      102400   83  Linux
/dev/sdb2          206848      411647      102400   83  Linux
/dev/sdb3          411648      821247      204800   83  Linux LVM
/dev/sdb4          821248     8388607     3783680    5  Extended
```

Typically, we want the extended partition to take whatever disk space is
left, since otherwise the space is wasted and rendered unusable due to
the MBR convention used by BIOS. However, if UEFI is used, the usage of
GUID (Globally Unique ID) Partition Tables (GPT) which lifts this
restriction.

Now, we have to add a logical partition on the disk. Since all 4
partitions are in use, the system defaults to adding a new logical
partition on the extended partition automatically. We add the new
partition and then change the partition type (using `t`) to *Linux LVM*
by providing the code `8e`.

``` console
Command (m for help): n
All primary partitions are in use
Adding logical partition 5
First sector (823296-8388607, default 823296):
Using default value 823296
Last sector, +sectors or +size{K,M,G} (823296-8388607, default 8388607): +100M
Partition 5 of type Linux and of size 100 MiB is set

Command (m for help): t
Partition number (1-5, default 5):
Hex code (type L to list all codes): 8e
Changed type of partition 'Linux' to 'Linux LVM'

Command (m for help): p

Disk /dev/sdb: 4294 MB, 4294967296 bytes, 8388608 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x9287c46d

Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048      206847      102400   83  Linux
/dev/sdb2          206848      411647      102400   83  Linux
/dev/sdb3          411648      821247      204800   83  Linux LVM
/dev/sdb4          821248     8388607     3783680    5  Extended
/dev/sdb5          823296     1028095      102400   8e  Linux LVM
```

Now we save the configuration and use the `partprobe` command to update
the kernel’s information about the available partitions.

``` console
Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
# partprobe
```

## Extending the Volume Group

Next let us consider we want to extend the existing LVM partition on
*/dev/sdb3*. Then, we use the `vgextend` command to extend the LVM
partition. It requires a Volume Group name, and a Physical device path,
with the intention of adding the entire physical device to the VG. This
is a *shortcut* since we don’t have to create a PV on the device to be
added (*/dev/sdb5*), as when all conditions are met, the `vgextend`
command itself creates a PV on the disk, after which the disk is
extended.

``` console
# vgextend vgCLI /dev/sdb5
Physical volume "/dev/sdb5" successfully created.
Volume group "vgCLI" successfully extended
```

Now, we can extend the logical volume to take up as much space on the VG
as we want. We can confirm that our VG has been extended with the `vgs`
command, and we can see which PVs are included in it (and confirm if
*/dev/sdb5* is present in it), using the `pvs` command.

``` console
# vgs
VG               #PV #LV #SN Attr   VSize   VFree
centos_cliserver   1   2   0 wz--n- <19.00g      0
vgCLI              2   1   0 wz--n- 292.00m 192.00m
# pvs
PV         VG               Fmt  Attr PSize   PFree
/dev/sda2  centos_cliserver lvm2 a--  <19.00g      0
/dev/sdb2                   lvm2 ---  100.00m 100.00m
/dev/sdb3  vgCLI            lvm2 a--  196.00m  96.00m
/dev/sdb5  vgCLI            lvm2 a--   96.00m  96.00m
```

## Extending the LV and the File System

The LV is extended using the `lvextend` command, that takes as an
argument:

<span>lM<span>0.85</span></span> **Options** &**Description**  
**-L** \&Absolute size in KiB/MiB/GiB  
**-l** \&The number of logical extents **OR** a percentage of either the
VG size, the LV/PV size or the free space available in the VG, etc.  
**-r** \&Also resizes the file system on the LV, irrespective of file
system.  

The complete `lvextend` command then looks like:

``` console
# lvextend -l +100%FREE -r /dev/vgCLI/lvCLI
Phase 1 - find and verify superblock...
Phase 2 - using internal log
- zero log...
- scan filesystem freespace and inode maps...
- found root inode chunk
Phase 3 - for each AG...
- scan (but don't clear) agi unlinked lists...
- process known inodes and perform inode discovery...
- agno = 0
- agno = 1
- agno = 2
- agno = 3
- process newly discovered inodes...
Phase 4 - check for duplicate blocks...
- setting up duplicate extent list...
- check for inodes claiming duplicate blocks...
- agno = 0
- agno = 1
- agno = 2
- agno = 3
No modify flag set, skipping phase 5
Phase 6 - check inode connectivity...
- traversing filesystem ...
- traversal finished ...
- moving disconnected inodes to lost+found ...
Phase 7 - verify link counts...
No modify flag set, skipping filesystem flush and exiting.
Size of logical volume vgCLI/lvCLI changed from 100.00 MiB (25 extents) to 292.00 MiB (73 extents).
Logical volume vgCLI/lvCLI successfully resized.
meta-data=/dev/mapper/vgCLI-lvCLI isize=512    agcount=4, agsize=6400 blks
=                       sectsz=512   attr=2, projid32bit=1
=                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=25600, imaxpct=25
=                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=855, version=2
=                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 25600 to 74752
```

The last few lines are the output from the `mkfs.xfs` command which is
used to resize the file system on the disk. Had the filesystem been XFS,
the `resize2fs` utility would’ve been used instead. The result of the
operation can be verified using the `df -h` command and checking the
file system size.

``` console
# df -h /dev/vgCLI/lvCLI
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/vgCLI-lvCLI  289M   16M  274M   6% /LVM
```

