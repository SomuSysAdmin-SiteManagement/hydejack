---
layout : post
title : 'Configuring an FTP Server for anonymous download'
tags : [RHCSA, RHCSA_mod, Configuring FTP Services, Understanding FTP Configuration]
categories: [RHCSA]
---


If not already available, we should ensure that the **vsftpd** service
is installed and enabled with:

``` console
# yum install -y vsftpd
# systemctl enable vsftpd
```

We can then list the configuration files for vsftpd using:

``` console
# rpm -qc vsftpd
/etc/logrotate.d/vsftpd
/etc/pam.d/vsftpd
/etc/vsftpd/ftpusers
/etc/vsftpd/user_list
/etc/vsftpd/vsftpd.conf
```

## vsftpd.conf

The default vsftpd.conf file has an option called `anonymous_enable=YES`
which allows anonymous downloads.

<span>rM<span>0.64</span></span> **Options** &**Description**  
**anonymous\_enable=YES** \&Allows anonymous users to download files
from their own directory.  
**local\_enable=YES** \&Allows authenticated users to download files
from their own home directory. It needs the boolean `ftp_home_dir -->
on` via SELinux to work.  
**write\_enable=YES** \&Allows authenticated users to write files to
their own directory.  

There is nothing that needs to be changed in `httpd.conf` as the default
settings are configured according to our needs. Now, we can restart the
*vsftpd* service.

``` console
# systemctl restart vsftpd
# systemctl status vsftpd
● vsftpd.service - Vsftpd ftp daemon
Loaded: loaded (/usr/lib/systemd/system/vsftpd.service; enabled; vendor preset: disabled)
Active: active (running) since Fri 2017-12-22 15:09:30 IST; 6s ago
Process: 11516 ExecStart=/usr/sbin/vsftpd /etc/vsftpd/vsftpd.conf (code=exited, status=0/SUCCESS)
Main PID: 11517 (vsftpd)
CGroup: /system.slice/vsftpd.service
└─11517 /usr/sbin/vsftpd /etc/vsftpd/vsftpd.conf

Dec 22 15:09:30 vmPrime.somuVMnet.com systemd[1]: Starting Vsftpd ftp daemon...
Dec 22 15:09:30 vmPrime.somuVMnet.com systemd[1]: Started Vsftpd ftp daemon.
```

We should be aware as an Admin about where the file are stored for the
anonymous FTP user. This is the home directory of the FTP user, to find
which we use:

``` console
# grep ftp /etc/passwd
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
```

On our system, it’s configured to be on (the default directory) of
`/var/ftp`. Now all we need is an FTP client such as **lftp** which lets
us browse the FTP directories. We can install it using `yum install -y
lftp`. We can connect as an anonymous ftp user using: `lftp localhost`.
