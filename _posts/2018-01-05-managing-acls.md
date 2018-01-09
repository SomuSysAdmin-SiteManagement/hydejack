---
layout : post
title : 'Managing ACLs'
tags : [RHCSA, RHCSA_mod, Managing Permissions, Understanding ACLs]
categories: [RHCSA]
---


Let us consider a case where the account group needs read only access to
the sales directory and vice versa. Of course we don’t want to grant any
access to others. Now, we need to assign a secondary group to the
*sales* and *account* directory without removing their respective
primary groups. This can be done using ACLs.

When the ACLs haven’t been setup yet, the `getfacl` command shows the
same information as the `ls -l` command.

``` console
# getfacl account
file: account
owner: lori
group: account
flags: -st
user::rwx
group::rwx
other::---
```

The `flags: -st` parameter shows us whether the SetUID, SetGID and
Sticky Bit are set, in that order (sst). Since the GID is set, as is the
sticky bit, but not the UID, the flags shows up as `-st`.

Note that the ACLs are copying over the current permission settings to
the ACL. Thus, before setting ACLs, we need to ensure our permissions
are exactly the way we want them to be. If we try to change the
permission settings after creating the ACLs, we will end up in a
mess.

| **Option** |    | **Description**                                                 |
| :--------: | :-: | :-------------------------------------------------------------- |
|   **-m**   | \- | Modify, followed immediately by what needs to be modified.      |
|   **-R**   | \- | Recursive, i.e., apply to all files currently in the directory. |

To set the sales group to have read access on the account folder and to
check the permissions, we use:

``` console
# setfacl -R -m g:sales:r account
# getfacl account
file: account
owner: lori
group: account
flags: -st
user::rwx
group::rwx
group:sales:r--
mask::rwx
other::---
```

This only takes care of the items already present in the *account*
directory, but not the new files that will be created in it. For that,
we need to setup a default ACL. NOTE that default ACLs do no need to be
applied recursively.

``` console
# setfacl -m d:g:sales:r account
getfacl account
file: account
owner: lori
group: account
flags: -st
user::rwx
group::rwx
group:sales:r--
mask::rwx
other::---
default:user::rwx
default:group::rwx
default:group:sales:r--
default:mask::rwx
default:other::---
```

The default ACL for the user, groups, etc are created from the current
permission settings of the directory. If we make a directory in it, the
following will be the ACL for it:

``` console
# cd account/
# mkdir 2017
# getfacl 2017
file: 2017
owner: root
group: account
flags: -s-
user::rwx
group::rwx
group:sales:r--
mask::rwx
other::---
default:user::rwx
default:group::rwx
default:group:sales:r--
default:mask::rwx
default:other::---
```

Now, if we were to make a new file in this directory, we get the
following ACL for it: (Note that a file, by definition, can’t have any
default settings unlike directories, since they are leaf objects that
can’t have any children to apply the default permissions).

``` console
# cd 2017/
# touch testFile
# getfacl testFile
file: testFile
owner: root
group: account
user::rw-
group::rwx          #effective:rw-
group:sales:r--
mask::rw-
other::---
```

Note that the mask has become active. This is because in case of files,
we never want to grant execute permissions by default. So, even though
in the POSIX permission, the group is granted `rwx` permission set, the
mask of `rw-` is superimposed on it, and the union of the two, (i.e.,
`rw-`) is the effective permissions on the file for the owner group.

Thus, we need to remember that whenever we set ACLs on a directory we
need two commands: one to set the ACL for the existing files, and the
other for the default ACLs for the new files that can be created in the
directory. Contrastingly, ACLs need to be set with only one command in
case of files (when manually setting them to a file; inheritance of ACLs
is automatic).

## history

The `history` command shows us all the commands that were executed on
the terminal (since last boot).
