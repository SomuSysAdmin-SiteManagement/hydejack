---
layout : post
title : 'Understanding ACLs'
tags : [RHCSA, RHCSA_mod, Managing Permissions, Understanding ACLs]
categories: [RHCSA]
---


Access Control Lists are a way to permit allocation of permissions to a
file/directory to more than one user or group. Normally, a file has only
one user who is owner and only one group with a certain permission set.
With ACLs it’s possible to set different set of permissions to different
groups/users\! They can also be used to setup the default permissions
for all newly created files/directories for any directory.

## Mount options

To actually user ACLs, the **acl mount** options must be set. This can
be done using either of `/etc/fstab` or **systemd**.

### tune2fs for Ext file systems

**tune2fs** is an utility that lets us set adjustable file system
parameters for the default Ext file system of RHEL/CentOS 7. This makes
it possible to put the mount options *not* in a seperate file, but make
it a property of the file system itself. Thus, if the file system is
ever migrated to another server, the properties will be moved with it
and not need to be set up again\!

### XFS

In XFS, there is no need for mounting options as it’s a default mount
option.

## Commands

There are two primary commands to use ACLs: **setfacl** - (Set File
Access Control Lists) and **getfacl** - (Get File Access Control Lists)
are the two commands used to work with ACLs.

``` console
$ setfacl -m g:sales:rx /data/account
```

The critical part of this command is the part `g:sales:rx` which tells
us that the group *sales* is getting the read and execute permissions.
To allow read & write permissions for the user *lisa* we can use :
`u:lisa:rw`.

### Default ACL

After setting any ACL we also need to set up a default ACL that’ll
handle all items that we’re going to create later in the future in that
folder. This is done by specifying a `d` (default) in the `setfacl`
command:

``` console
$ setfact -m d:g:account:rx /data/account
```

