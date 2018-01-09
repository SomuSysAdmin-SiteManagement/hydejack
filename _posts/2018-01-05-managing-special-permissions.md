---
layout : post
title : 'Managing Special Permissions'
tags : [RHCSA, RHCSA_mod, Managing Permissions, Managing Special Permissions]
categories: [RHCSA]
---


Let us consider a shell script resides in the home directory of user
*lisa* that deletes everything on the system:

``` console
#!/bin/bash
echo "Hi, do you wanna play a game?!"
read

rm -rf /
```

Generally, whenever a non-admin is going to execute this script, the
only thing that’ll be deleted would be user files (in directories the
user has write access to), specifically the user home directory and the
shared directories where the user has write access.

``` console
# chmod u+s game
# ls -l | grep game
-rwsr--r--. 1 root root 77 Nov 22 19:48 game
```

However, if the file were to be executed with the UID of an admin user,
with root access, the `rm -rf /` command would cause critical damage.
This is why both SetUID and SetGID are so dangerous\!

## Finding a file with a particular set of permissions

The find command is capable of finding a bunch of files where the
permission set matches a format. We do this by:

``` console
# find / -perm /4000
find: ‘/proc/2998/task/2998/fd/6’: No such file or directory
find: ‘/proc/2998/task/2998/fdinfo/6’: No such file or directory
find: ‘/proc/2998/fd/6’: No such file or directory
find: ‘/proc/2998/fdinfo/6’: No such file or directory
/usr/bin/fusermount
/usr/bin/su
/usr/bin/umount
/usr/bin/chage
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/staprun
/usr/bin/mount
/usr/bin/pkexec
/usr/bin/crontab
/usr/bin/passwd
/usr/sbin/pam_timestamp_check
/usr/sbin/unix_chkpwd
/usr/sbin/usernetctl
/usr/lib/polkit-1/polkit-agent-helper-1
/usr/lib64/dbus-1/dbus-daemon-launch-helper
/usr/libexec/abrt-action-install-debuginfo-to-abrt-cache
/home/lisa/game
```

Only special files are given this privilege, such as the
`/usr/bin/passwd` binary executable. This is the files that enables us
to change the password for a user. Now, to accomplish this the password
has to be stored in an encrypted form in the `/etc/shadow` file with the
following permissions:

``` console
# ls -l /etc/shadow
----------. 1 root root 1122 Nov 25 16:55 /etc/shadow
```

Thus, the *passwd* binary needs the root user privileges to make the
`/etc/shadow` file temporarily editable by itself.

## Setting Group ID for a directory

Let us consider the following scenario. User lisa is a member of the
*account* group and the folder */data* has the following permissions:

``` console
#ls -l
total 0
drwxrwx---. 2 lori account 6 Nov 25 17:35 account
drwxrwx---. 2 lisa sales   6 Nov 25 17:26 sales
# su - lisa
Last login: Sat Nov 25 17:31:57 IST 2017 on pts/0
$ cd /data/account/
$ touch lisa1
$ ls -l
total 0
-rw-rw-r--. 1 lisa lisa 0 Nov 25 17:35 lisa1
```

The file */data/account/lisa1* has it’s group owner set to the personal
group of lisa. This means that the other members of the group *account*
don’t have write permission to that file. This is not acceptable in a
shared group folder where multiple users have to edit the same file.

``` console
$ su - laura
Password:
Last login: Thu Nov 16 13:42:44 IST 2017 on pts/0
$ cd /data/account
$ echo "Added a line" >> lisa1
-bash: lisa1: Permission denied
```

This is why **Set group id** for a folder is so useful - so that each
file created by the user in that directory, is by default editable by
all the users in that group\!

``` console
# ls -l
total 0
drwxrwx---. 2 lori account 19 Nov 25 17:35 account
drwxrwx---. 2 lisa sales    6 Nov 25 17:26 sales
# chmod g+s account
# ls -l
total 0
drwxrws---. 2 lori account 19 Nov 25 17:35 account
drwxrwx---. 2 lisa sales    6 Nov 25 17:26 sales
# su - lisa
Last login: Sat Nov 25 17:35:39 IST 2017 on pts/0
$ cd /data/account
$ touch lisa2
$ ls -l
total 0
-rw-rw-r--. 1 lisa lisa    0 Nov 25 17:35 lisa1
-rw-rw-r--. 1 lisa account 0 Nov 25 17:45 lisa2
$ echo "line added by lisa" >> lisa2
$ su - laura
Password:
Last login: Sat Nov 25 17:41:55 IST 2017 on pts/0
$ cd /data/account
$ echo "line added by laura" >> lisa2
$ cat lisa2
line added by lisa
line added by laura
```

## Sticky Bit

When the sticky bit has been set the user can only delete a file if
he/she’s the owner of the file or the owner of the directory. This makes
it invaluable in cases of shared directories, where each user needs
write access to all files, and thus automatically gets the permission to
delete any file he can write to\!

In the case of the *account* directory, the owner of the file `lisa1` is
lisa. Thus, the user laura can’t delete it.

``` console
# ls -l
total 0
drwxrws---. 2 lori account 32 Nov 25 17:45 account
drwxrwx---. 2 lisa sales    6 Nov 25 17:26 sales
# ls -l account
total 4
-rw-rw-r--. 1 lisa lisa     0 Nov 25 17:35 lisa1
-rw-rw-r--. 1 lisa account 39 Nov 25 17:46 lisa2
# chmod +t account
# ls -l
total 0
drwxrws--T. 2 lori account 32 Nov 25 17:45 account
drwxrwx---. 2 lisa sales    6 Nov 25 17:26 sales
# su - laura
Last login: Sat Nov 25 17:53:25 IST 2017 on pts/0
$ cd /data/account
$ ls -l
total 4
-rw-rw-r--. 1 lisa lisa     0 Nov 25 17:35 lisa1
-rw-rw-r--. 1 lisa account 39 Nov 25 17:46 lisa2
$ rm -f lisa1
rm: cannot remove ‘lisa1’: Operation not permitted
$ su - lori
Password:
$ cd /data/account
$ rm -f lisa1
$ ls -l
total 4
-rw-rw-r--. 1 lisa account 39 Nov 25 17:46 lisa2
```

However, the user laura is able to delete it as she’s the owner of the
(parent) folder *account*.

## Lowercase ’s’ or ’t’ vs Uppercase in permissions

The uppercase in case of *Set UserID/ Set GroupID/ Sticky Bit* indicates
that that particular user/group or others don’t have execute permissions
on that directory. If however, they do have execute permissions then the
’S’/’T’ is converted to lowercase, to indicate that there is an ’x’
hidden behind the ’s’ or ’t’.

``` console
# mkdir test
# ls -l
total 0
drwxrws--T. 2 lori account 19 Nov 25 17:57 account
drwxrws--T. 2 lisa sales    6 Nov 25 17:26 sales
drwxr-xr-x. 2 root root     6 Nov 25 18:15 test
# chmod 3770 *
# ls -l
total 0
drwxrws--T. 2 lori account 19 Nov 25 17:57 account
drwxrws--T. 2 lisa sales    6 Nov 25 17:26 sales
drwxrws--T. 2 root root     6 Nov 25 18:15 test
# chmod o+x,g-x test
# ls -l
total 0
drwxrws--T. 2 lori account 19 Nov 25 17:57 account
drwxrws--T. 2 lisa sales    6 Nov 25 17:26 sales
drwxrwS--t. 2 root root     6 Nov 25 18:15 test
```

An example of a folder with sticky bit set by default is `/tmp` where
all users must be allowed to write files, but we don’t want users to
delete the files of other users.
