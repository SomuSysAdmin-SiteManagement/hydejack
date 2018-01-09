---
layout : post
title : 'Managing File System mounts in a systemd Environment'
tags : [RHCSA, RHCSA_mod, Managing and Understanding the Boot Procedure, Managing File System mounts in a systemd Environment]
categories: [RHCSA]
---


Other than using `/etc/fstab`, systemd also provides a way to mount file
systems. Further, not all file systems are mounted (or available) using
`/etc/fstab`. The file systems that can be mounted using systemd (called
**mount units**) can be obtained by:

``` console
# ls *.mount
dev-hugepages.mount            sys-kernel-config.mount
dev-mqueue.mount               sys-kernel-debug.mount
proc-fs-nfsd.mount             tmp.mount
proc-sys-fs-binfmt_misc.mount  var-lib-nfs-rpc_pipefs.mount
sys-fs-fuse-connections.mount
```

These contain the specifications for certain file systems that need to
be mounted at all times, such as `/tmp`. The contents of `tmp.mount` is:

``` bash
[Unit]
Description=Temporary Directory
Documentation=man:hier(7)
Documentation=http://www.freedesktop.org/wiki/Software/systemd/APIFileSystems
ConditionPathIsSymbolicLink=!/tmp
DefaultDependencies=no
Conflicts=umount.target
Before=local-fs.target umount.target
After=swap.target

[Mount]
What=tmpfs
Where=/tmp
Type=tmpfs
Options=mode=1777,strictatime

# Make 'systemctl enable tmp.mount' work:
[Install]
WantedBy=local-fs.target
```

While the unit specification is very generic, the `[Mount]` and
`[Install]` specifications are very important. The `What` defines the
file system to be mounted, the `Where` clause defines the location to
mount the file system. The file system type is *tmpfs* and there are
certain mount options as well. The *Install* section defines that
`local-fs.target` needs this mount point to work, which in turn makes it
possible to mount this file system using `systemctl`.

If we want a custom mount file like this, we have to put it in
`/etc/systemd/system` directory. A bare-bones mount unit file would look
like:

``` bash
# Mount unit for /dev/vgPrime/lvPrime

[Unit]
Description="My test mount"

[Mount]
what=/dev/vgPrime/lvPrime
Where=/myLv
Type=xfs

[Install]
WantedBy=multi-user.target
```

Then we mount the disk and check its status using:

``` console
# systemctl start myLv.mount
# systemctl status myLv.mount -l
● myLv.mount - "My test mount"
Loaded: loaded (/etc/systemd/system/myLv.mount; disabled; vendor preset: disabled)
Active: active (mounted) since Wed 2017-12-20 10:51:59 IST; 3s ago
Where: /myLv
What: /dev/mapper/vgPrime-lvPrime
Process: 3510 ExecMount=/bin/mount /dev/vgPrime/lvPrime /myLv -t xfs (code=exited, status=0/SUCCESS)

Dec 20 10:51:59 vmPrime.somuVMnet.com systemd[1]: Mounting "My test mount"...
Dec 20 10:51:59 vmPrime.somuVMnet.com systemd[1]: Mounted "My test mount".
```

Now, to ensure that the disk is auto-mounted when the multi-user.target
is loaded, we need to add a symlink to it in the *wants* directory for
that target. This is achieved by:

``` console
# systemctl enable myLv.mount
Created symlink from /etc/systemd/system/multi-user.target.wants/myLv.mount to /etc/systemd/system/myLv.mount.
```

This makes sure that every time the *multi-user.target* is active, the
file system *myLv* is auto-mounted.
