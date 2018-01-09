---
layout : post
title : 'Creating and Managing Users'
tags : [RHCSA, RHCSA_mod, Managing Users and Groups, Creating and Managing Users]
categories: [RHCSA]
---


## Adding users

The default command for adding users on RHEL is
`useradd`.

| **Option** | **Description**                                                           |
| :--------: | :------------------------------------------------------------------------ |
|   **-e**   | Expiration date in the format YYYY-MM-DD. Sets the date on which the user |
|            | account will be disabled.                                                 |
|   **-c**   | Comment that sets the contents of the GECOS field.                        |
|   **-s**   | Sets the default shell of the user. For example, a C programmer can use a |
|            | shell such as TCSH.                                                       |

``` console
$ sudo useradd -c "New Test User" -s /bin/tcsh -e 2017-12-31 laura
[sudo] password for somu:
$ # To verify the addition of the new user
$ tail -n 1 /etc/passwd
laura:x:1001:1001:New Test User:/home/laura:/bin/tcsh
```

### id command

The `id` command prints the real and effective user information.

``` console
$ id
uid=1000(somu) gid=1000(somu) groups=1000(somu) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```
