---
layout : post
title : 'Changing file ownership'
tags : [RHCSA, RHCSA_mod, Managing Permissions, Changing file ownership]
categories: [RHCSA]
---


Let us consider a directory `/data` with the following structure:

``` console
$ ls -l
total 0
drwxr-xr-x. 2 root root 6 Nov 21 14:50 accounts
drwxr-xr-x. 2 root root 6 Nov 21 14:50 sales
```

The user ’root’ has `rwx` permissions (all), while the group ’root’ as
well as others have only ’rw’ (read/execute) permissions. None of them
can write to the files in either of these directories by default.

## chgrp

Now, it’s reasonable to assume that everyone in sales should have write
access to the sales directory, while everyone in accounts department
should have write access to the group directory. Thus, we set these
permissions using the `chgrp` command and setting the appropriate groups
as the group-owner of these directories.

``` console
# ls -l
total 0
drwxr-xr-x. 2 root root 6 Nov 21 14:50 account
drwxr-xr-x. 2 root root 6 Nov 21 14:50 sales
# chgrp sales sales
# chgrp account account
# ls -l
total 0
drwxr-xr-x. 2 root account 6 Nov 21 14:50 account
drwxr-xr-x. 2 root sales   6 Nov 21 14:50 sales
```

The syntax for `chgrp` is `chgrp <group> <file/directory>`.

## chown

The HoDs of these individual groups should be assigned as the owners of
these directories. To assign them as such, we use the `chown` command.

``` console
# chown lori account
# chown lisa sales
# ls -l
total 0
drwxr-xr-x. 2 lori account 6 Nov 21 14:50 account
drwxr-xr-x. 2 lisa sales   6 Nov 21 14:50 sales
```

The syntax for the `chown` command is : `chown <user>
<file/directory>`.  
To change both the user and the group at once, the syntax becomes :  
`chown <user>:<group> <file/directory>`.
