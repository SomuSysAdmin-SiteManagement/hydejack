---
layout : post
title : 'Making the File System'
tags : [RHCSA, RHCSA_mod, Managing Partitions, Making the File System]
categories: [RHCSA]
---


Just after being created, a partition contains no file system, and thus
no files can yet be stored on it. We have to create an appropriate file
system using:

## mkfs

This is actually a whole bunch of different utilities that are grouped
together under the same command. They
are:

``` bash
mkfs         mkfs.btrfs   mkfs.cramfs  mkfs.ext2    mkfs.ext3    mkfs.ext4    mkfs.fat     mkfs.minix   mkfs.msdos   mkfs.vfat    mkfs.xfs
```

Since the default file system is `XFS`, `mkfs.xfs` is the default file
system utility.

``` console
# mkfs.xfs --help
mkfs.xfs: invalid option -- '-'
unknown option --
Usage: mkfs.xfs
/* blocksize */     [-b log=n|size=num]
/* metadata */      [-m crc=0|1,finobt=0|1,uuid=xxx]
/* data subvol */   [-d agcount=n,agsize=n,file,name=xxx,size=num,
(sunit=value,swidth=value|su=num,sw=num|noalign),
sectlog=n|sectsize=num
/* force overwrite */   [-f]
/* inode size */    [-i log=n|perblock=n|size=num,maxpct=n,attr=0|1|2,
projid32bit=0|1]
/* no discard */    [-K]
/* log subvol */    [-l agnum=n,internal,size=num,logdev=xxx,version=n
sunit=value|su=num,sectlog=n|sectsize=num,
lazy-count=0|1]
/* label */     [-L label (maximum 12 characters)]
/* naming */        [-n log=n|size=num,version=2|ci,ftype=0|1]
/* no-op info only */   [-N]
/* prototype file */    [-p fname]
/* quiet */     [-q]
/* realtime subvol */   [-r extsize=num,size=num,rtdev=xxx]
/* sectorsize */    [-s log=n|size=num]
/* version */       [-V]
devicename
<devicename> is required unless -d name=xxx is given.
<num> is xxx (bytes), xxxs (sectors), xxxb (fs blocks), xxxk (xxx KiB),
xxxm (xxx MiB), xxxg (xxx GiB), xxxt (xxx TiB) or xxxp (xxx PiB).
<value> is xxx (512 byte blocks).
```

The **block size (-b)** should be larger when primarily dealing with
large files. This way, lesser blocks are used, and the administration of
large files becomes easier. The **inode size (-i)** should be larger if
it is known beforehand that lots of advanced stuff that stores metadata
in inodes will be used. The *label (-L)* sets the name for that
filesystem. To actually create the file system, we use the
command:

``` console
meta-data=/dev/sdb1              isize=512    agcount=4, agsize=65536 blks
=                       sectsz=512   attr=2, projid32bit=1
=                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=262144, imaxpct=25
=                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
=                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

