---
layout : post
title : 'Understanding VNC'
tags : [RHCSA, RHCSA_mod, Configuring VNC Access, Understanding VNC]
categories: [RHCSA]
---


Virtual Network Computing (VNC) is a graphical desktop sharing system
that lets a remote user control a local server. On our server runs a
Graphical User Interface (GUI) provided by **X-Server** (which handles
all graphical I/O and graphics processing interfaces). While the
X-Server is capable of providing a single GUI to the local user, it’s
also the system service responsible for providing VNC interfaces.

VNC doesn’t take over the existing GUI session. Instead, it provides a
second GUI session for the remote user, which also runs on top of
X-Server. For this to be possible, there must be a VNC server process
running on our server.

Let us consider a remote user connects for a VNC session using VNC
viewer via `user@host localhost:1`, through SSH (and is thus received by
`sshd`). The SSH protocol is needed as VNC itself is insecure. Sending
clear-text id and passwords over the network is extremely ill-advised,
and thus the user should initiate an encrypted session with the ssh
process. Once the user has authenticated with SSH, a session can be
established from SSH to the VNC server.

![VNC
Connection<span label="fig:4 VNC Connection"></span>](RHCSA/Mod4/chapters/4.26.a)

Next, a connection will be established from the VNC server to the VNC
session running for that user. This session will provide access to the
X-Server, thus making it possible for the remote user to with a
full-blown graphical user interface running on the server.
