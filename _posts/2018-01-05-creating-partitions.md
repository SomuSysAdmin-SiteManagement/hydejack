---
layout : post
title : 'Creating Partitions'
tags : [RHCSA, RHCSA_mod, Managing Partitions, Creating Partitions]
categories: [RHCSA]
---


To add a new disk to our OS, first we need to verify the storage disks
that are available. For this we use the **proc** filesystem in `/proc/`.
It acts as an interface to everything that’s happening in the kernel.
The `/proc/partitions` file contains a listing of all the disks and
partitions that are currently existing.

``` console
$ cat /proc/partitions
major minor  #blocks  name

8        0   20971520 sda
8        1       2048 sda1
8        2     499712 sda2
8        3   15634432 sda3
8       16   10485760 sdb
11        0    8491008 sr0
253        0    3903488 dm-0
253        1    1953792 dm-1
253        2    1953792 dm-2
253        3    7815168 dm-3
```

While *sda* is the first hard disk, the device *sdb* is a newly added
one - the second hard disk avaiable in the computer. The partitions are
marked by a number after the device name - *sda1, sda2 and sda3*. The
second hard disk doesn’t have any partitions yet.

## fdisk

**fdisk** is an old partitioning tool that has been revised for RHEL 7.
Running the `fdisk` utility on `/dev/sdb`, the location which the OS
uses to designate the second hard disk yeilds:

``` console
# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0xf11ab429.

Command (m for help):
```

It tells us that the disk doesn’t contain any partitions (since it’s
brand new). The fdisk utility offers us a bunch of commands, among which
we’ll use:

<span>lM<span>0.84</span></span> **Options** &**Description**  
**p** \&Print partition table  
**n** \&Add a new partition  
**w** \&Write the table to disk and exit  

### p command

The p option gives us the current disk layout:

``` bash
Command (m for help): p

Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xf11ab429

Device Boot      Start         End      Blocks   Id  System

Command (m for help):
```

The device name is *sdb* and its size is 10.7GB. This gives it 20
Million sectors, since the size of each sector is 512B. Now we add a new
partition on the disk:

``` bash
Command (m for help): n
Partition type:
p   primary (0 primary, 0 extended, 4 free)
e   extended
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-20971519, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-20971519, default 20971519): +100M
Partition 1 of type Linux and of size 100 MiB is set

Command (m for help):
```

The default option at the prompt can be selected by simply pressing the
enter key. Since there are no physical partitions already available, and
since we should always choose the option to add physical partitions
whenever possible, we add a new physical partition. It asks us for the
starting sector, the default of which is 2048. The first 2MBs are used
to store metadata. Next, we mark the end of the partition using a
relative size: in this case, of 100MiB (\(1024^2\)B). The size has to be
specified with uppercase K/M/G to not be misconstrued to any other unit.
Printing the partition table now shows:

``` bash
Device Boot         Start         End      Blocks   Id  System
/dev/sdb1            2048      206847      102400   83  Linux
```

This new partition can then be written to the disk using `w`.

``` bash
Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Now when we view the partitions in `/proc/partitions` we see:

``` console
# cat /proc/partitions
major minor  #blocks  name

8        0   20971520 sda
8        1       2048 sda1
8        2     499712 sda2
8        3   15634432 sda3
8       16   10485760 sdb
8       17     102400 sdb1
11        0    8491008 sr0
253        0    3903488 dm-0
253        1    1953792 dm-1
253        2    1953792 dm-2
253        3    7815168 dm-3
```

The disk now has a sdb1 partition of size 100MiB. This indicates that
the disk is now ready to accept a filesystem. In case an error is show
along the lines of "*the device is busy*", the system probably needs a
restart.
