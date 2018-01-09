---
layout : post
title : 'Understanding Apache Configuration'
tags : [RHCSA, RHCSA_mod, Managing HTTP Services, Understanding Apache Configuration]
categories: [RHCSA]
---


The **HTTP Daemon (httpd)** is the apache web server process. To find
out more about the process, we use:

``` console
# which httpd
/usr/sbin/httpd
# rpm -qf /usr/sbin/httpd # Obtaining the name of the package which installed httpd.
httpd-2.4.6-67.el7.centos.6.x86_64
# rpm -qc httpd
/etc/httpd/conf.d/autoindex.conf
/etc/httpd/conf.d/userdir.conf
/etc/httpd/conf.d/welcome.conf
/etc/httpd/conf.modules.d/00-base.conf
/etc/httpd/conf.modules.d/00-dav.conf
/etc/httpd/conf.modules.d/00-lua.conf
/etc/httpd/conf.modules.d/00-mpm.conf
/etc/httpd/conf.modules.d/00-proxy.conf
/etc/httpd/conf.modules.d/00-systemd.conf
/etc/httpd/conf.modules.d/01-cgi.conf
/etc/httpd/conf/httpd.conf
/etc/httpd/conf/magic
/etc/logrotate.d/httpd
/etc/sysconfig/htcacheclean
/etc/sysconfig/httpd
```

The last command, `rpm -qc httpd` shows us the configuration files for
the *httpd* process. There are some config files for httpd in
`/etc/sysconfig` directory and some in `/etc/httpd` directory.

The `/etc/sysconfig` directory has a file called *httpd* which has some
basic configuration for the web server, and this can be used to manage
start-up parameters for apache. Thus, whenever there needs to be
anything different whie starting the apache web server, this file should
be edited.

The important part of the `httpd` configuration is stored in
`/etc/httpd`. It’s contents are:

``` console
# ls -l /etc/httpd
total 0
drwxr-xr-x. 2 root root  37 Dec 20 15:36 conf
drwxr-xr-x. 2 root root  82 Dec 20 15:36 conf.d
drwxr-xr-x. 2 root root 146 Dec 20 15:36 conf.modules.d
lrwxrwxrwx. 1 root root  19 Dec 20 15:36 logs -> ../../var/log/httpd
lrwxrwxrwx. 1 root root  29 Dec 20 15:36 modules -> ../../usr/lib64/httpd/modules
lrwxrwxrwx. 1 root root  10 Dec 20 15:36 run -> /run/httpd
```

The most important of the configuration files is stored in
`/etc/httpd/conf/httpd.conf`. It contains all the parameters that might
need to be changed to customize the configuration of our apache
environment. Some of the important parameter passed to the web server
from this file are:

<span>lM<span>0.58</span></span> **Options** &**Description**  
**Listen 80** \&Tells the HTTP server which port to *listen on* (i.e.,
wait for incoming TPC connections) for HTTP Services.  
**include conf.modules.d/\*.conf** \&Loads the contents of the
`conf.modules.d` directory.  

The inclusion of the `/etc/httpd/conf.modules.d` directory is due to the
fact that apache has a modular configuration. Both files in the
*conf.modules.d* and *conf.d* are included in this configuration. The
contents of the `conf.d` are:

``` console
# ls /etc/httpd/conf.d
autoindex.conf  README  userdir.conf  welcome.conf
```

Some RPMs that deal with the apache web server sometimes drop
configuration files in this directory that add another branch of
functionality to our web server. This particular folder, *conf.d* is
used to house generic configurations. The folder *conf.modules.d*
however, contains the configuration for several modules. These include
things like the proxy module.

Sometimes, the apache update may cause a new version of the *httpd.conf*
to appear, in which case the user config will still be available at
*httpd.conf.rpmsave* in the same directory. This is not something
specific to apache - yum does this to any config file that has to be
overwritten in the process of an upgrade.
