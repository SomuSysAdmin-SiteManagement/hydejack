---
layout : post
title : 'Understanding Ownership: Users, Groups and Others'
tags : [RHCSA, RHCSA_mod, Managing Permissions, Understanding Ownership: Users, Groups and Others]
categories: [RHCSA]
---


The permissions for any file/folder in Linux can be viewed by using `ls
-l` :

``` console
$ cd /home
$ ls -l
total 4
drwx------.  3 lisa lisa    78 Nov 15 21:32 lisa
drwx------.  3 1002 sales   78 Nov 15 21:36 rogue
drwx------. 19 somu somu  4096 Nov 20 19:33 somu
drwx------.  5 2002   101  128 Nov 19 23:36 testUsr
```

The format of the output is :  
`<Permissions> <link-count of a file/no of files in directory>
<owner>`  
`<group-owner> <file-size> <date & time of last modification>
<file-name>`

## Permissions

The first character in the permissions section, is the file type. The
following file types are the most common:

| **Notation** | **Description**    |
| :----------: | :----------------- |
|    **d**     | A directory        |
|    **-**     | A regular file     |
|    **l**     | A symlink/softlink |

The rest of the permissions section is divided into three parts: the
user’s permissions, the group’s permissions and other’s permissions. The
first 3 characters after the first one represents the user’s
permissions, the next 3 the group’s and the final the other’s. The
possible values of these are:

| **Notation** | **Description**                          |
| :----------: | :--------------------------------------- |
|    **r**     | Read the file/directory                  |
|    **w**     | Write to the file/directory              |
|    **x**     | Execute the file/Access to the directory |
|    **-**     | Permission NOT granted                   |

## Ownership

In linux, every file and directory (which is a *special* kind of file
has an owner, as well as an associated group-owner. The owner is the
user who created the file (unless specifically changed). The filesystem
defines the permission set for the **owner**, the associated **group**
and the rest of the users, called **others**.

While determining what set of permissions a user has to a file, linux
first checks if the user is the owner. If so, the associated permissions
are applied. If not, linux checks to see if the user belongs to the
group which owns the file. If so, the group permissions on the file are
granted. If both of these fail, then the user is determined to be
’*other*’ and the appropriate permissions applied. Of course, this
requires the algorithm to be *exit-on-match*.
