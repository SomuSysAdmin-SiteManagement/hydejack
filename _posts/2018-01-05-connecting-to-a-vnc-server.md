---
layout : post
title : 'Connecting to a VNC Server'
tags : [RHCSA, RHCSA_mod, Configuring VNC Access, Configuring a VNC Server]
categories: [RHCSA]
---


To connect to the VNC server, we need the **vncviewer** utility. The
basic syntax of the `vncviewer` command is: `vncviewer -via
<vnc-username>@<vnc-host> <vnc-host>:1`. So, when testing it on
localhost, we’ll use:

``` console
# vncviewer -via vncuser@localhost localhost:1

TigerVNC Viewer 64-bit v1.8.0
Built on: 2017-12-01 23:20
Copyright (C) 1999-2017 TigerVNC Team and many others (see README.txt)
See http://www.tigervnc.org for information on TigerVNC.
vncuser@localhost's password:

Sat Dec 23 19:13:38 2017
DecodeManager: Detected 1 CPU core(s)
DecodeManager: Decoding data on main thread
CConn:       connected to host localhost port 33955
t CConnection: Server supports RFB protocol version 3.8
CConnection: Using RFB protocol version 3.8
CConnection: Choosing security type VeNCrypt(19)
CVeNCrypt:   Choosing security type TLSVnc (258)
e
Sat Dec 23 19:13:41 2017
CConn:       Using pixel format depth 24 (32bpp) little-endian rgb888
CConn:       Using Tight encoding
CConn:       Enabling continuous updates
```

When the above command is executed, first SSH authentication occurs,
followed by VNC authentication. If both authentications are passed, a
remote session on the server for that particular user (i.e., *vncuser*
in our case) is started.
