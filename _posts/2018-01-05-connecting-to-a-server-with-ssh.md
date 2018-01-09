---
layout : post
title : 'Connecting to a Server with SSH'
tags : [RHCSA, RHCSA_mod, Connecting to a RHEL Server, Connecting to a Server with SSH]
categories: [RHCSA]
---


To connect to a server we use the `ssh` command. The Syntax is: `ssh
<server-ip>`.

``` console
$ ssh 192.168.152.129
somu@192.168.152.129's password:
Last login: Mon Nov 13 12:37:26 2017 from 192.168.152.128
```

The default SSH port is **22**. To connect to SSH on a different port
(common when server is exposed to the internet), is `ssh -p
<port-number> <server-ip>`. Note that *if root login is disabled on the
server,* we must also provide the username to login as. The syntax then
becomes : `ssh -p <port-number> <username>@<server-ip>`.

``` console
$ ssh -p 2022 sander@ldap.rhatcertification.com
```
