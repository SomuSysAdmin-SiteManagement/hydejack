---
layout : post
title : 'Using Booleans'
tags : [RHCSA, RHCSA_mod, Managing SELinux, Using Booleans]
categories: [RHCSA]
---


To handle booleans, we need two commands: `getsebool` and `setsebool`.
The command to list all possible SELinux Boolean Switches on a
particular system is given by:

``` console
# getsebool -a
abrt_anon_write --> off
abrt_handle_event --> off
abrt_upload_watch_anon_write --> on
...
zoneminder_anon_write --> off
zoneminder_run_sudo --> off
```

To find an appropriate boolean for something (e.g., FTP), we use
grepping:

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

For example, if we want to turn on the switch for `ftpd_use_nfs -->
off`, all we need to do is:

``` console
# setsebool ftpd_use_nfs on
# getsebool ftpd_use_nfs
ftpd_use_nfs --> on
```

These changes are however temporary in nature, and thus lost after a
restart. To make these changes permanent, we need to use:

``` console
# setsebool -P ftpd_use_nfs on
# getsebool ftpd_use_nfs
ftpd_use_nfs --> on
```

This particular operation takes considerably more time since the policy
has to be modified.
