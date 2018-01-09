---
layout : post
title : 'Creating a LUKS Encrypted Partition'
tags : [RHCSA, RHCSA_mod, Managing Partitions, Creating a LUKS Encrypted Partition]
categories: [RHCSA]
---


To create the encrypted partition, we once again use the `fdisk`
utility. Since we want to put this partition on the `/dev/sdb` device,
we use:

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
/dev/sdb1         2048     2099199     1048576   83  Linux
```

At first we ensure that sufficient disk space is available via printing
the existing file system details on the disk. In this example, we can
see that the number of available sectors on the disk is \(20,971,520\)
while the End=\(2,099,199\) tells us that only those sectors are used
till now. Thus, we can add a new encrypted partition. The initial
procedure is same as creating a normal partition:

``` console
Command (m for help): n
Partition type:
p   primary (1 primary, 0 extended, 3 free)
e   extended
Select (default p): p
Partition number (2-4, default 2):
First sector (2099200-20971519, default 2099200):
Using default value 2099200
Last sector, +sectors or +size{K,M,G} (2099200-20971519, default 20971519): +100M
Partition 2 of type Linux and of size 100 MiB is set

Command (m for help): p

Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xf11ab429

Device Boot      Start         End      Blocks   Id  System
/dev/sdb1         2048     2099199     1048576   83  Linux
/dev/sdb2      2099200     2303999      102400   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.

# partprobe /dev/sdb
# cat /proc/partitions
major minor  #blocks  name

8        0   20971520 sda
8        1       2048 sda1
8        2     499712 sda2
8        3   15634432 sda3
8       16   10485760 sdb
8       17    1048576 sdb1
8       18     102400 sdb2
11       0    8491008 sr0
253      0    3903488 dm-0
253      1    1953792 dm-1
253      2    1953792 dm-2
253      3    7815168 dm-3
```

The `partprobe /dev/sdb` command updates the kernel parititon table,
i.e., tells the kernel about the changes in the partition table on that
device so that the kernel can provide the required functionality.

## Formatting the new partition

To encrypt the new partition we use the `cryptsetup` command. An
argument of `luksFormat` is required to specify the encryption
formatting. We encrypt `/dev/sdb2` by:

``` console
# cryptsetup luksFormat /dev/sdb2

WARNING!
========
This will overwrite data on /dev/sdb2 irrevocably.

Are you sure? (Type uppercase yes): YES
Enter passphrase:
Verify passphrase:
#
```

Now that our encrypted partition has been created, we need to open it to
use it. For this we can use a custom-made dedicated mount point such as
`/secret`. Then, we have to open the partition using `cryptsetup
luskOpen` before we can mount it. At this point, we also have to provide
a name for the partition. Finally, we go to the `/dev/mapper` directory
to ensure that the new partition has been successfully loaded.

``` console
# cryptsetup luksOpen /dev/sdb2 secret
Enter passphrase for /dev/sdb2:
[root@vmPrime /]# cd /dev/mapper
[root@vmPrime mapper]# ls
centos-home  centos-root  centos-swap  centos-var  control  secret
```

Since we can see the required partition in the `/dev/mapper` directory,
we can be sure that the partition was opened successfully\! The complete
path of our partition is `/dev/mapper/secret`. Now we can proceed to
create a file system on it: (let’s assume we want to format the disk as
ext4)

``` console
# mkfs.ext4 /dev/mapper/secret
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=1024 (log=0)
Fragment size=1024 (log=0)
Stride=0 blocks, Stripe width=0 blocks
25168 inodes, 100352 blocks
5017 blocks (5.00%) reserved for the super user
First data block=1
Maximum filesystem blocks=33685504
13 block groups
8192 blocks per group, 8192 fragments per group
1936 inodes per group
Superblock backups stored on blocks:
8193, 24577, 40961, 57345, 73729

Allocating group tables: done
Writing inode tables: done
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done
```

The encrypted partition is now ready to be mounted and used. We do this
by:

``` console
# mkdir /secret
# mount /dev/mapper/secret /secret
# cd /secret
```

While we normally might never need to close the encrypted partition, if
for example we have a partition that’s stored on an external device, we
first need to unmount it, followed by performing a `cryptsetup
luksClose`.

``` console
[root@vmPrime ~]# umount /secret
[root@vmPrime ~]# cryptsetup luksClose /dev/mapper/secret
[root@vmPrime ~]# ls -l /dev/mapper
total 0
lrwxrwxrwx. 1 root root       7 Dec  8 06:51 centos-home -> ../dm-3
lrwxrwxrwx. 1 root root       7 Dec  8 06:51 centos-root -> ../dm-0
lrwxrwxrwx. 1 root root       7 Dec  8 06:51 centos-swap -> ../dm-1
lrwxrwxrwx. 1 root root       7 Dec  8 06:51 centos-var -> ../dm-2
crw-------. 1 root root 10, 236 Dec  8 06:51 control
```

The folder `/secret` within `/dev/mapper` has disappeared as it’s been
saved to the original encrypted partition where no one can access the
data without decryption.

Now, if we want to automount the partition, we need to add an entry for
it in the `/etc/fstab` file:

``` bash
/dev/mapper/secret  /secret ext4    defaults    1 2
```

However, the above code work since at the time the `/etc/fstab` file is
processes, there is no `/dev/mapper/secret` directory during boot since
the file system on the encrypted partition won’t be open yet\! To do
this, we need to create/edit the `/etc/crypttab` file, with the
following contents:

``` bash
secret  /dev/sda2   none
```

The first value in the file is the name that’s to be assigned to the
partition in the `/dev/mapper/` directory, the second is the device
name, and the third, the name of password file to be used. Since we’re
not using a password file, we’ve left it as *none*. Thus, the system
will prompt for the passphrase at boot to open and mount the LUKS
encrypted device. Now, to confirm the auto mounting of the device, we
need to reboot.
