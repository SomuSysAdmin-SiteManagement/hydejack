---
layout : post
title : 'Configuring Automount'
tags : [RHCSA, RHCSA_mod, Connecting to a LDAP Server, Configuring Automount]
categories: [RHCSA]
---


To use automount, the *automount service* in the **autofs** package
needs to be installed. The primary configuration file is
`/etc/auto.master`. To automount the `/etc/guests` folder from a Samba
server, we just need to specify in the file that:

``` bash
/home/guests    /etc/auto.guests
```

Now we can add the mount options in its own individual file
*auto.guests*, such that quick mounting and unmounting is possible.

### Configuration on the Samba Server

The Samba server containing the `data` directory needs to have the
following configuration in its `/etc/samba/smb.conf` :

``` bash
[data]
comment = LDAP Users' home directories
path = /home/guests
public = yes
writable = no
```

## NFS Server Automounting

In the case of a NFS mounted directory, the *auto.guests* file would
look like:

``` bash
*   -rw nfsServer.domain.com:/home/guests/&
```

In case of either servers, the syntax remains the same. First we provide
the name of the directory (`*`), then the mounting options (e.g., `-rw`
in case of NFS) and finally the path to the real directory that has to
be mounted on the local file system from that server.
