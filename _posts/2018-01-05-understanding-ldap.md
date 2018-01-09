---
layout : post
title : 'Understanding LDAP'
tags : [RHCSA, RHCSA_mod, Connecting to a LDAP Server, Understanding LDAP]
categories: [RHCSA]
---


LDAP is an easy way to provide centralized authentication from a server.
This way, many computers can be connected to a single LDAP server and
the user accounts (and permissions) have to be set up only once\!

**LDAP** stands for *Lightweight Directory Access Protocol*. It connects
us to a hierarchical directory server. In the hierarchy (e.g.,
server.rhatcertification.com), there are top level domains such as
*.com*, subdomain (rhatcertification) and leaf objects (lisa). Even
though the structure is similar to DNS, the notation of LDAP is
different. For every container object, we write `dc=<objectName>` (*dc*
\(\rightarrow\) Domain Component) and for leaf objects, it becomes
`cn=<objectName>` (*cn* \(\rightarrow\) Common Name). The complete
format then becomes *cn=lisa,dc=rhatcertificaton,dc=com*.

An important part of connecting to an LDAP server is the **base
context**. The base context, like the search domain of DNS, is the
starting point where our client should look for objects. In this case,
the base context is `dc=rhatcertification,dc=com`. Thus, for logging in
to a server, the *cn*(lisa) is searched for within the base context.

## /bin/login

The `login` service is used whenever the user requires authentication to
connect to anything.

## ldd

The `ldd` command (List Dynamic Dependencies) prints all the shared
libraries required by a program.

``` console
$ ldd /bin/login
linux-vdso.so.1 =>  (0x00007ffc333e3000)
libpam.so.0 => /lib64/libpam.so.0 (0x00007f85cad8a000)
libpam_misc.so.0 => /lib64/libpam_misc.so.0 (0x00007f85cab86000)
libaudit.so.1 => /lib64/libaudit.so.1 (0x00007f85ca95d000)
libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f85ca736000)
libc.so.6 => /lib64/libc.so.6 (0x00007f85ca373000)
libdl.so.2 => /lib64/libdl.so.2 (0x00007f85ca16e000)
libcap-ng.so.0 => /lib64/libcap-ng.so.0 (0x00007f85c9f68000)
libpcre.so.1 => /lib64/libpcre.so.1 (0x00007f85c9d06000)
/lib64/ld-linux-x86-64.so.2 (0x0000558e5f0bd000)
libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f85c9ae9000)
```

The PAM library shown above (libpam) is akin to a plugin that adds
additional functionality to the `login` utility (as well as several
others). PAM stands for *Plugable Authentication Modules*. The
configuration files for the authentication module is stored in
`/etc/pam.d` directory. The `/etc/pam.d/login` is the configuration file
for `login` utility.

## PAM config file syntax

The PAM config files are each named after the services that require the
usage of PAM. For example, the config file for the *login* service is
called `/etc/pam.d/login`. Each file lists a bunch of rules in the
syntax : `<service-type> <control> <module-path>
<arugments>`.

### Service Type

| **Service Type** | **Description**                                                           |
| :--------------- | :------------------------------------------------------------------------ |
| **auth**         | Deals with user authentication via password (or other means like keys).   |
| **account**      | Non-authentication based account management.                              |
| **password**     | Updating the authentication token of the user.                            |
| **session**      | Modules listed here are used for setup/cleanup of a service for the user. |

### PAM Module Controls

| **Control**    | **Description**                                                                      |
| :------------- | :----------------------------------------------------------------------------------- |
| **requisite**  | Immediately causes failure when the module returns a status that isn’t ’success’.    |
| **required**   | If the service returns a non-success status, then the operation fails ultimately,    |
|                | but only after the modules below it are invoked. This is to prevent a person with    |
|                | malicious intent from gaining knowledge of which module failed.                      |
| **sufficient** | If a *sufficient* module returns a ’success’ status, the other modules below it that |
|                | are also a part of ’sufficient’ management group will not be invoked. In case of     |
|                | failure, another module listed ’sufficient’ in the stack below it must succeed for   |
|                | the operation to succeed.                                                            |
| **optional**   | Only causes failure if the rule stack contains only optional modules and all fail.   |
| **include**    | For the given service type, include all lines of that type from the provided         |
|                | configuration file.                                                                  |
| **substack**   | Same as *include* but when *done* and *die* actions are evaluated, they only cause   |
|                | skipping of the substack.                                                            |

The `login` config file in `/etc/pam.d` contains the line:

``` bash
auth       substack     system-auth
```

**NOTE** *: the entry for pam\_ldap requires that the host should be
able to use LDAP, which requires* `pam_ldap` *to be installed, and*
`authconfig-tui` *to be executed.*

The `system-auth` file has rules for the common login procedure for any
any process that deals with user authentication. This file in turn
contains the lines :

``` bash
auth        sufficient    pam_unix.so nullok try_first_pass
...
auth        sufficient    pam_ldap.so use_first_pass
```

The line `auth sufficient pam_unix.so` tells the system to look at the
System login local authentication mechanism (`pam_unix.so`). If that is
not successful, the system is instructed to use the LDAP PAM mechanism
(in `pam_ldap.so`). Thus, the login process is contacting an LDAP server
and trying to verify if the user account exists on that server.

![LDAP
Authentication<span label="fig:1"></span>](RHCSA/Mod1/chapters/1.6.a%20LDAP%20Authentication)

Next, the LDAP configuration file (`/etc/nslcd.conf`) is read, which
contains the URI of the LDAP Server (`ldap://server.rhatcert.com`).
Finally, the client is able to connect to the LDAP server where there is
a LDAP hierarchy that it can log into.
