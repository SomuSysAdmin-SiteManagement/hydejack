---
layout : post
title : 'Creating a Basic Web Site'
tags : [RHCSA, RHCSA_mod, Managing HTTP Services, Understanding Apache Configuration]
categories: [RHCSA]
---


One of the most important configuration settings in the `httpd.conf`
file is the **DocumentRoot**, which sets the directory under which all
the requests for documents are served. If the DocumentRoot is changed,
certain settings in SELinux need to be changed as well\! The default
value of this is set to `/var/www/html`.

Let us put a basic html file inside this directory:

``` html
<html>
<head>
<title>Homepage!</title>
<head>
<body>
<h1>Hello, World!</h1>
<body>
</html>
```

To view this, (even during an SSH session) we can use *elinks*, which is
a text based browser. First, we have to start the HTTP daemon (and
enable it so that it auto-starts after each reboot):

``` console
# systemctl start httpd
# systemctl enable httpd
Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.
# systemctl status -l httpd
● httpd.service - The Apache HTTP Server
Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
Active: active (running) since Wed 2017-12-20 17:26:08 IST; 11s ago
Docs: man:httpd(8)
man:apachectl(8)
Main PID: 5802 (httpd)
Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
CGroup: /system.slice/httpd.service
├─5802 /usr/sbin/httpd -DFOREGROUND
├─5806 /usr/sbin/httpd -DFOREGROUND
├─5807 /usr/sbin/httpd -DFOREGROUND
├─5808 /usr/sbin/httpd -DFOREGROUND
├─5809 /usr/sbin/httpd -DFOREGROUND
└─5810 /usr/sbin/httpd -DFOREGROUND

Dec 20 17:26:08 vmPrime.somuVMnet.com systemd[1]: Starting The Apache HTTP Server...
Dec 20 17:26:08 vmPrime.somuVMnet.com systemd[1]: Started The Apache HTTP Server.
```

The contents of the `/var/www/html` directory are now available on
localhost. To view the webpages, we use:

``` console
# elinks http://localhost
```

