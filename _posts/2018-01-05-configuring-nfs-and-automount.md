---
layout : post
title : 'Configuring NFS and Automount'
tags : [RHCSA, RHCSA_mod, Connecting to a LDAP Server, Configuring NFS and Automount]
categories: [RHCSA]
---


## yum search

The `yum` utility provides a searching function that searches the name,
description, summary and url of all the packages available for a
keyword.

``` console
# yum search nfs
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
* base: mirror.digistar.vn
* extras: mirror.dhakacom.com
* updates: mirror.digistar.vn
=================================== N/S matched: nfs ====================================
libnfsidmap.i686 : NFSv4 User and Group ID Mapping Library
libnfsidmap.x86_64 : NFSv4 User and Group ID Mapping Library
libnfsidmap-devel.i686 : Development files for the libnfsidmap library
libnfsidmap-devel.x86_64 : Development files for the libnfsidmap library
nfs-utils.x86_64 : NFS utilities and supporting clients and daemons for the kernel NFS server
nfs4-acl-tools.x86_64 : The nfs4 ACL tools
nfsometer.noarch : NFS Performance Framework Tool
nfstest.noarch : NFS Testing Tool
```

## Creating an NFS Server

The **nfs-utils** package is needed to setup an NFS server. The NFS
configuration file is called `/etc/exports`. It specifies which file
systems are exported to remote hosts (from the NFS server’s perspective)
and provides their respective mounting options. The contents of the
*exports* file has to follow the syntax :

``` bash
/data   *(rw,no_root_squash)
```

Here, `/data` is the name of the directory to be hosted on the NFS, with
read/write permissions from all (`*`) IP addresses on the local network
(since NFS only works on the local network). In cases it’s not desirable
to have the local machine’s administrator act as the admin of the NFS
server, then the way to perform this is called root squashing. In our
case, we turn it off as we want the root user to retain administrative
privileges on the NFS server as well.

## Starting the NFS server

To start the service corresponding to the NFS server, we use the
`systemctl` command.

``` console
$ systemctl start nfs
```

In case the service fails to start, the following command can provide
hints about what went wrong :

``` console
# systemctl status -l nfs
nfs-server.service - NFS server and services
Loaded: loaded (/usr/lib/systemd/system/nfs-server.service; disabled; vendor preset: disabled)
Active: active (exited) since Tue 2017-11-21 10:00:04 IST; 24s ago
Process: 3178 ExecStart=/usr/sbin/rpc.nfsd $RPCNFSDARGS (code=exited, status=0/SUCCESS)
Process: 3173 ExecStartPre=/bin/sh -c /bin/kill -HUP `cat /run/gssproxy.pid` (code=exited, status=0/SUCCESS)
Process: 3172 ExecStartPre=/usr/sbin/exportfs -r (code=exited, status=1/FAILURE)
Main PID: 3178 (code=exited, status=0/SUCCESS)
CGroup: /system.slice/nfs-server.service

Nov 21 10:00:04 ldapserver.somuvmnet.local systemd[1]: Starting NFS server and services...
Nov 21 10:00:04 ldapserver.somuvmnet.local exportfs[3172]: exportfs: Failed to stat /data: No such file or directory
Nov 21 10:00:04 ldapserver.somuvmnet.local systemd[1]: Started NFS server and services.
```

Now we can see the mounting status of the NFS server hosted at
localhost, using the `showmount -e localhost` command. Further, the NFS
folder can be mounted manually using the `mount` command.

``` console
# showmount -e localhost
Export list for localhost:
/data *
# mount localhost:/data /mnt/nfs
# ls /nfs
localNFSfile1  localNFSfile2  localNFSfile3
```

## Automounting NFS

For automounting NFS, we create a new entry in `auto.master` for a file
`/etc/auto.nfs` :

``` bash
/mnt/nfs    /etc/auto.nfs
```

This file contains the following mounting details:

``` bash
files   -rw localhost:/data/
```

Thus, when the user enters the *files* directory within the *nfs*
directory, he’ll find the same files as in the `/data` directory of
localhost.

``` console
# cd nfs
# ls
# ls -lha
total 0
drwxr-xr-x. 2 root root  0 Nov 21 11:59 .
drwxr-xr-x. 3 root root 17 Nov 21 11:59 ..
# cd files
# ls
nfs1  nfs2  test1
# cd ..
# ls
files
```

Note that the `/nfs/files` directory isn’t actually created before the
user tries to enter the *files* directory.
