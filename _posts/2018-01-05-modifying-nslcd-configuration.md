---
layout : post
title : 'Modifying nslcd Configuration'
tags : [RHCSA, RHCSA_mod, Connecting to a LDAP Server, Configuring NFS and Automount]
categories: [RHCSA]
---


## Naming Services LDAP Client Daemon

The `nslcd` is a service that connects the local file system to the
external LDAP server. The status of the `nslcd` can be checked using:

``` console
$ systemctl status nslcd
nslcd.service - Naming services LDAP client daemon.
Loaded: loaded (/usr/lib/systemd/system/nslcd.service; enabled; vendor preset: disabled)
Active: active (running) since Mon 2017-11-20 12:03:06 IST; 5h 59min ago
Process: 1108 ExecStart=/usr/sbin/nslcd (code=exited, status=0/SUCCESS)
Main PID: 1151 (nslcd)
CGroup: /system.slice/nslcd.service
1151 /usr/sbin/nslcd
```

### /etc/nsswitch.conf

For every LDAP user, their identity needs to be known to the local
system. This is based on the configuration stored in
`/etc/nsswitch.conf`. In this file, there is a line:

``` bash
passwd: files   sss ldap
```

This represents the order in which the sources of user account are
searched for user related information. `sss` is an older service no
longer used by RHEL-7. Finally, it looks for the information in LDAP
using `nslcd`.

### PAM using nslcd

PAM is responsible for the actual authentication system that ensures the
LDAP server is known to the authentication mechanism. This entire
process is achieved using *nslcd*.

## /etc/nslcd.conf

This file contains the information stored by using the `authconfig-gtk`
command and has options to configure the `nslcd` such that the LDAP
server can be connected to and
used.

| **Option**     | **Description**                          | **Example Value**                   |
| :------------- | :--------------------------------------- | :---------------------------------- |
| uri            | The Uniform Resource Identifier of the   | ldap://server.rhatcertification.com |
|                | LDAP Server.                             |                                     |
| base           | The base context of the LDAP Server      | dc=rhatcertification,dc=com         |
| ssl            | Whether to use SSL/TLS. If start\_tls is | start\_tls                          |
|                | given, via the use of StartTLS, an       |                                     |
|                | insecure connection is upgraded to a     |                                     |
|                | secure one.                              |                                     |
| tls\_cacertdir | Location of the downloaded certificate   | `/etc/openldap/cacerts`             |
|                | of the LDAP Server.                      |                                     |

In case of any problems with using LDAP, the `/var/log/messages` file
may contain hints that may indicate what’s wrong.
