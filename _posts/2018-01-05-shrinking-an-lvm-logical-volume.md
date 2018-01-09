---
layout : post
title : 'Shrinking an LVM logical Volume'
tags : [RHCSA, RHCSA_mod, Managing LVM Logical Volumes, Growing an LVM Logical Volume]
categories: [RHCSA]
---


The shrinking operation of an LVM needs to be supported by the file
system on board the LV. This is not the case for XFS as it doesn’t
support shrinking. **To shrink a LV, the file system on it must be
unmounted first\!** The size of the FS then must be reduced before
shrinking the LV. To resize the Ext4 FS, we use `resize2fs` utility,
which is the *xt2/Ext3/Ext4 File System Resizer*.

If we directly try to run the resize2fs on the disk, we’ll be advised to
run `e2fsck` utility to check file system consistency, i.e., if the file
system has any problems with it. So, the commands to reduce the LV are:

``` console
# e2fsck -f /dev/mapper/vgCLI-lvCLI
e2fsck 1.42.9 (28-Dec-2013)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
lvCLI: 11/25688 files (9.1% non-contiguous), 8896/102400 blocks
# resize2fs /dev/mapper/vgCLI-lvCLI 50M
resize2fs 1.42.9 (28-Dec-2013)
Resizing the filesystem on /dev/mapper/vgCLI-lvCLI to 51200 (1k) blocks.
The filesystem on /dev/mapper/vgCLI-lvCLI is now 51200 blocks long.
# lvreduce -L 50M /dev/mapper/vgCLI-lvCLI
Rounding size to boundary between physical extents: 52.00 MiB.
WARNING: Reducing active logical volume to 52.00 MiB.
THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce vgCLI/lvCLI? [y/n]: y
Size of logical volume vgCLI/lvCLI changed from 100.00 MiB (25 extents) to 52.00 MiB (13 extents).
Logical volume vgCLI/lvCLI successfully resized.
```

Now, if there weren’t any errors, we should be able to mount the file
system on board the LV.

``` console
# mount /dev/mapper/vgCLI-lvCLI /LVM/
# mount | grep ^/dev
/dev/mapper/centos_cliserver-root on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sda1 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/mapper/vgCLI-lvCLI on /LVM type ext4 (rw,relatime,seclabel,data=ordered)
# df -h /dev/vgCLI/lvCLI
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/vgCLI-lvCLI   45M  1.1M   40M   3% /LVM
```

In the last line we see that the file system size has been properly
reduced.

## Reduce both File system and LV in a single step

It is possible to shrink the LV and the on-board FS in a single command:
(*The* `-r` *option automatically resizes the FS before shrinking the
LV*).

``` console
# umount /LVM
# lvreduce -L 35M -r /dev/vgCLI/lvCLI
Rounding size to boundary between physical extents: 36.00 MiB.
fsck from util-linux 2.23.2
lvCLI: 11/13832 files (18.2% non-contiguous), 6886/51200 blocks
resize2fs 1.42.9 (28-Dec-2013)
Resizing the filesystem on /dev/mapper/vgCLI-lvCLI to 36864 (1k) blocks.
The filesystem on /dev/mapper/vgCLI-lvCLI is now 36864 blocks long.

Size of logical volume vgCLI/lvCLI changed from 52.00 MiB (13 extents) to 36.00 MiB (9 extents).
Logical volume vgCLI/lvCLI successfully resized.
# mount /dev/mapper/vgCLI-lvCLI /LVM
# mount | grep ^/dev
/dev/mapper/centos_cliserver-root on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sda1 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/mapper/vgCLI-lvCLI on /LVM type ext4 (rw,relatime,seclabel,data=ordered)
# df -h /dev/mapper/vgCLI-lvCLI
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/vgCLI-lvCLI   31M  783K   28M   3% /LVM
```

Note however, that this method won’t work all the time on all file
systems, due to the fact that the target FS must also support reduction
via `lvreduce -r`. Thus, while the `-r` won’t work on XFS, it works just
fine on Ext4.
