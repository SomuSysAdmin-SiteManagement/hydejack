---
layout : post
title : 'Managing Automount in a systemd Environment'
tags : [RHCSA, RHCSA_mod, Managing and Understanding the Boot Procedure, Managing File System mounts in a systemd Environment]
categories: [RHCSA]
---


To auto-mount a file system, the procedure is similar to manually
mounting a file system. Just like the latter, we need to create a *Mount
unit file* for the file system. Then, we want the file system to be
mounted whenever a certain activity occurs in the auto-mount directory.
To do this with *myLv.mount*, we first need to disable it. Once that is
done, we also need to disconnect the mount.

``` console
# systemctl disable myLv.mount
Removed symlink /etc/systemd/system/multi-user.target.wants/myLv.mount.
# systemctl stop myLv.mount
# systemctl status myLv.mount
● myLv.mount - "My test mount"
Loaded: loaded (/etc/systemd/system/myLv.mount; disabled; vendor preset: disabled)
Active: inactive (dead)
Where: /myLv
What: /dev/vgPrime/lvPrime

Dec 20 10:51:59 vmPrime.somuVMnet.com systemd[1]: Mounting "My test mount"...
Dec 20 10:51:59 vmPrime.somuVMnet.com systemd[1]: Mounted "My test mount".
Dec 20 11:08:37 vmPrime.somuVMnet.com systemd[1]: Unmounting "My test mount"...
Dec 20 11:08:38 vmPrime.somuVMnet.com systemd[1]: Unmounted "My test mount".
```

## Automount Unit file

The auto-mounting of a directory needs an auto-mount unit file, which is
named following the syntax: `<mountFileName>.automount`. Since our LV
has a mount file called *myLv.mount*, we have to use the name
`myLv.automount`. The automount unit file is relatively much simpler
than its manual mounting counterpart.

``` bash
[Unit]
Description = myLv Automount

[Automount]
Where = /myLv

[Install]
WantedBy = multi-user.target
```

At this point, we can enable and start the automount unit:

``` console
# systemctl enable myLv.automount
Created symlink from /etc/systemd/system/multi-user.target.wants/myLv.automount to /etc/systemd/system/myLv.automount.
# systemctl start myLv.automount
# systemctl status myLv.automount
● myLv.automount - myLv Automount
Loaded: loaded (/etc/systemd/system/myLv.automount; enabled; vendor preset: disabled)
Active: active (waiting) since Wed 2017-12-20 11:21:51 IST; 7s ago
Where: /myLv

Dec 20 11:21:51 vmPrime.somuVMnet.com systemd[1]: Set up automount myLv Automount.
Dec 20 11:21:51 vmPrime.somuVMnet.com systemd[1]: Starting myLv Automount.
```

Another (quicker) way to verify the automount would be to use:

``` console
# mount | grep myLv
systemd-1 on /myLv type autofs (rw,relatime,fd=33,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=13344)
/dev/mapper/vgPrime-lvPrime on /myLv type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
```

## Difference between enabling Mount vs Automount Units

The act of enabling the *.mount* files ensures that the `autofs` process
mounts the file, while when we enable the *.automount* files makes
systemd mount the files. Thus, the latter is the method that is
preferred, since systemd is a newer system of initializing services and
automounts, and is future-proof.
