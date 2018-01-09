---
layout : post
title : 'User Properties'
tags : [RHCSA, RHCSA_mod, Managing Users and Groups, User Properties]
categories: [RHCSA]
---


## Username

A typical user info in the `/etc/passwd` file consists of the login
information of several users, each with the following details :-
`somu:x:1000:1000:Somu:/home/somu:/bin/bash`. Here, Somu is the
username, the *x* in the second field references that a password has
been stored for that username in the `/etc/shadow` file. The file
contains the (one-way) encrypted password as well as several password
related information such as password expiration dates, etc. Since the
`/etc/shadow` file is only readable by the root user, it minimizes the
security risk. Generally, only real user accounts need a password and
system users (accounts used by processes to execute) don’t.

### /etc/shadow

While the `/etc/shadow` file contains the password of an user in an
encrypted format, if the user account is new and doesn’t yet have any
password assigned to it, then the entry for it in /etc/shadow looks
like:

``` console
$ cat /etc/shadow | grep lisa
lisa:!!:17485:0:99999:7:::
```

The second entry (\!\!) is where the encrypted password is usually
stored. The double exclamation indicates that the *lisa* account hasn’t
set up a password yet.

## UID

Each user on the system is setup with a unique UserID (UID). The root
has a UID of 0, and normal users start with an UID of 1000 onwards.
There are a total of 64,000 UIDs available for 2.4 kernels, and 4
billion for 2.6 kernels.

## GID

On Linux, every user must be the member of at least one group, which is
known as the **primary group** of the user, stored in the `/etc/passwd`
file. On RHEL, that primary group has the same name as the username and
the user is the only member of that group by default (i.e., private
group). The list of Groups is stored in a file called `/etc/group`.

## GECOS or comment field

This is a comment field that can contain anything the admin deems
necessary. It generally contains information that makes the
identification of each user easier.

## Home Directory

The home directory refers to the location where the user is allowed to
store files. For services, this folder is important because it defines
the directory where the service can read and write files. While for
regular users the home directory is typically inside `/home`, for
services, they can be anywhere.

## Default Shell

This is the shell (or command) that is executed on login of the user.
The default value is `/bin/bash`.
