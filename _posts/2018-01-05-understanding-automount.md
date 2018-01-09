---
layout : post
title : 'Understanding Automount'
tags : [RHCSA, RHCSA_mod, Connecting to a LDAP Server, Understanding Automount]
categories: [RHCSA]
---


While it’s possible to have the LDAP users use local directories on the
server, generally an NFS hosts the home directories of these users.
Thus, we have to automount the home directories of these users as if
they’re part of the local file system.

Let us consider a system where automounting is enabled, and the user
wants to access a folder `/data/files`. If the folder `/data` is hosted
on a remote file system and monitored by the automount process (called
**autofs**), then there will have a file called `/etc/auto.master`
containing the line:

``` bash
/data   /etc/auto.data
```

The `/etc/auto.master` file only shows that the automount process
recognizes the `/data` directory as an automount directory. This merely
states that the mounting details for the data folder is present in its
own file called `/etc/auto.data`. That file will contain:

``` bash
files   -rw     nfsServer:/data
```

The files directory is a subdirectory of the `/data` directory, and thus
when the `/files` directory needs to be accessed, an NFS mounting
operation needs to occur, with read write access on the nfsServer’s
(hostname) `/data` directory. Even though the user will be working on
the NFS server, he/she will have no inkling of this happening behind the
scene.

![NFS
Automount<span label="fig:1"></span>](RHCSA/Mod1/chapters/1.7.a%20NFS%20Automount)

## Server selection for auto-mounting

Primarily two types of servers can accomplish the auto-mounting of home
directories for LDAP users - NFS and Samba servers. In case of NFS
server, the files will only be available on the local network. For
access through the Internet, a Samba server has to be used.

## Samba server’s CIFS protocol to automount

Let us consider an LDAP user *ldapuser1* who has his home directory
configured to  
`/home/guests/ldapuser1` in his user properties. When the user logs in,
there will be a system call to go to the home directory for the users,
which in turn calls `autofs` to mount the file system. It’ll consult the
`/etc/auto.master` file to find:

``` bash
/home/guests    /etc/auto.guests
```

If anyone wants to visit that directory, the process should consult the
`/etc/auto.guests` file, containing the mounting details with the UNC
(Universal Naming Convention) path of the actual Samba server on the
internet.

``` bash
*   -fstype=cifs,username=ldapusers,password=password\
://server.rhatcertification.com/data/&
```

So, if anyone goes to `*` (i.e., any directory in `/data/guests`), like
`/home/guests/ldapuser1` or `/home/guests/ldapuser2` and so on, a CIFS
(Common Internet File Sharing protocol, which uses Server Message Block
\[SMB, Used by Samba\]) mount needs to occur, specified by
`fstype=cifs`, with the given username and password. The address of the
server is then provided.

What is of particular importance here is the matching of `*` and `&`.
While the `*` wildcard selects whatever folder the user tried to enter,
the `&` in the address is replaced with the corresponding text from
user. Thus, if the user visits `/home/guests/ldapuser1`, the `*` is
replaced with `ldapuser1` and a matching folder is searched for on the
server.

![Samba
Automount<span label="fig:1"></span>](RHCSA/Mod1/chapters/1.7.b%20Samba%20Automount)
