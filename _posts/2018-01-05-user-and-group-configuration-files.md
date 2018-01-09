---
layout : post
title : 'User and Group configuration files'
tags : [RHCSA, RHCSA_mod, Managing Users and Groups, User and Group configuration files]
categories: [RHCSA]
---


Some of the important configuration files
are:

| **Option**               | **Description**                                                      |
| :----------------------- | :------------------------------------------------------------------- |
| **/etc/passwd**          | Contains several details of the user, other than the password.       |
| **/etc/shadow**          | Contains the password hash and password properties for the user.     |
| **/etc/group**           | Contains the names of all the groups along with a list of all users  |
|                          | in them.                                                             |
| **/etc/login.defs**      | Contains the values (definitions) of several parameters used to      |
|                          | create the user, such as password max days, min days, etc.           |
| **/etc/default/useradd** | Contains the default values for several useradd parameters.          |
| **/etc/skel**            | When a user’s home directory is created, the contents of             |
|                          | `/etc/skel` is copied there, with the appropriate group of the user. |
