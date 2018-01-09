---
layout : post
title : 'Understanding SELinux Modes and Policy'
tags : [RHCSA, RHCSA_mod, Managing SELinux, Understanding SELinux Modes and Policy]
categories: [RHCSA]
---


To configure SELinux at a basic level, there are three things that we
need to understand. The first of them is the SELinux Mode.

## SELinux Mode

The SELinux mode is obtained from a file called
`/etc/sysconfig/selinux`. There are three possible modes for this:
Enforcing, Permissive and disabled. The disabled mode can only be
specified while booting. This completely disables SELinux by ensuring
all the SELinux libraries that are normally loaded by the kernel won’t
be loaded at all. In fact, the difference is so drastic, it’s not
possible to switch between disabled and any other mode without
rebooting.

However, it is perfectly fine to toggle between enforcing and permissive
modes. The current SELinux mode is given by:

``` console
# getenforce
Enforcing
```

To change the SELinux mode, we use the command:

``` console
# setenforce Permissive
# getenforce
Permissive
```

Toggling between the permissive and enforcing modes can be extremely
useful for troubleshooting. Let us consider a scenario where we’re
setting up an FTP server, and it doesn’t work. This may be due to an
error in the FTP server config, or it’s being blocked by SELinux. To
make sure SELinux is not at fault, we switch to Permissive mode using
`setenforce Permissive` and try again. If it starts working, it was
being blocked by SELinux. Then we know where to look for the solution.
However, under all other circumstances, the SELinux mode should be set
to enforcing.

## Context and Policies

Everything on RHEL 7 has a context, which can be displayed by the
command:

``` console
# ls -Z
drwxr-xr-x. somu somu unconfined_u:object_r:user_home_t:s0 Desktop
drwxr-xr-x. somu somu unconfined_u:object_r:user_home_t:s0 Documents
drwxr-xr-x. somu somu unconfined_u:object_r:user_home_t:s0 Downloads
drwxr-xr-x. somu somu unconfined_u:object_r:audio_home_t:s0 Music
drwxr-xr-x. somu somu unconfined_u:object_r:user_home_t:s0 Pictures
drwxr-xr-x. somu somu unconfined_u:object_r:user_home_t:s0 Public
drwxr-xr-x. somu somu unconfined_u:object_r:user_home_t:s0 Templates
drwxr-xr-x. somu somu unconfined_u:object_r:user_home_t:s0 Videos
```

There are three parts to a context, with the delimiter `:` separating
them. The first is the *user* part, which is only for advanced SELinux
configurations. Next comes the *role* part, which again, is for advanced
SELinux configurations. Finally, we have the *type* part. This denotes
the kind of access that is allowed to files/directories.

Not only are there contexts on files, there are contexts on processes as
well, which can be viewed using `ps Zau`. Even ports have context
labels, viewed by using `netstat Ztulpen`. So, the idea is that every
file/process/port’s context is matched to a policy to grant/deny access.

## Booleans

Booleans are easy switches to enable or disable functionalities in a
policy. A list of all available booleans can be obtained with:

``` console
# getsebool -a
abrt_anon_write --> off
abrt_handle_event --> off
abrt_upload_watch_anon_write --> on
antivirus_can_scan_system --> off
...
zebra_write_config --> off
zoneminder_anon_write --> off
zoneminder_run_sudo --> off
```

We can filter the list and find only booleans that have ’ftp’ in their
boolean name using:

``` console
# getsebool -a | grep ftp
ftpd_anon_write --> off
ftpd_connect_all_unreserved --> off
ftpd_connect_db --> off
ftpd_full_access --> off
ftpd_use_cifs --> off
ftpd_use_fusefs --> off
ftpd_use_nfs --> off
ftpd_use_passive_mode --> off
httpd_can_connect_ftp --> off
httpd_enable_ftp_server --> off
tftp_anon_write --> off
tftp_home_dir --> off
```

For example, let us consider the boolean `ftpd_full_access --> off`
which doesn’t allows ftp servers to login to local user accounts and
have read/write access to all files subject to Discretionary Access
Control (DAC) mechanisms (permissions, ACLs, etc.). Another such boolean
is `ftp_home_dir --> off` which doesn’t allow users to login to their
home directories.

When certain functionalities are turned off, we should always check if
some boolean is turned off. In this case, since `ftp_home_dir` is off,
the users won’t be able to login to their home directories even though
*vsftpd* may be configured to do so, since SELinux will prevent it.
