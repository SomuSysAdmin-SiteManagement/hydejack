---
layout : post
title : 'Understanding the Need for SELinux'
tags : [RHCSA, RHCSA_mod, Managing SELinux, Understanding the Need for SELinux]
categories: [RHCSA]
---


SELinux stands for **Security Enhanced Linux**. Let us consider an
application that runs on a server, that provides a backdoor to an
attacker who can start a shell session on the server. This can be done
as the httpd user in the case of a vulnerability on the web server. Let
us consider the attacker uses the /tmp directory (which has rwxrwxrwx
permissions) for nefarious purposes. Now, we can’t take away
permissions, since some applications depend on the directory to have
universal permissions. We also can’t use a firewall, since it’d block
access to HTTP services. Finally, we can’t mount the file system with a
`NOEXEC` flag (which prevents the execution of scripts on that disk)
since sometimes applications use the `/tmp` directory to execute
scripts.

Under such circumstances, SELinux becomes extremely necessary, since it
permits us to set policies that define exactly what kind of access each
application has, and on which directories. Thus, it is critical to use
SELinux on any server that is connected to the internet.

## SELinux and Syscalls

Every operation on the server is occurring via syscalls. When enabled,
all of the syscalls are filtered through SELinux. SELinux can be in
either *enforcing* or *permissive* mode for this. Each system calls go
through an analysis against a policy that check whether the actions are
permitted. Let’s assume the action is not permitted, and a *avc:denied*
is returned. Now, several things will happen.

First, the event will go through **auditd**, and in any case, whenever
SELinux is enabled, *auditd* (configurable via `/etc/audit/auditd.conf`)
will write the event to the audit log (`/var/log/audit/audit.log`). This
is a very important source of information.

From there, if SELinux is set to *enforcing mode*, the syscall will be
immediately stopped. However, in *permissive mode*, it’ll go on, since
in permissive mode, everything is logged by auditd, but nothing is
stopped. Thus, the permissive mode allows us to analyse what is going on
without stopping syscalls, stopping which might lead to system crashes
and other unforeseen events.

Let us consider another example, where we have a webserver running on
localhost, which we try to access using `elinks`. Now, let the
webserver’s DocumentRoot be set to */blah* directory. `ls -Z` prints
the security context of every file or directory. On executing this
command on */blah*, we will probably find that the directory has the
wrong label.

Now when elinks tries to access the index file on the */blah* directory,
it’ll generate a *getattr* system call. If SELinux is in enforcing mode,
that’ll be stopped immediately.
