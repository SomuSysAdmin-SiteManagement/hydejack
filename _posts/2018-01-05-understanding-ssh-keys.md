---
layout : post
title : 'Understanding SSH keys'
tags : [RHCSA, RHCSA_mod, Connecting to a RHEL Server, Understanding SSH keys]
categories: [RHCSA]
---


To initiate the ssh connection, the **SSHD** service on the server is
contacted by the client. In order to confirm it’s identity, the server
responds with it’s own `/etc/ssh/ssh_host.pub` public key to the client.
When the client’s user has verified the key of the server, the public
key fingerprint gets stored in the clients `~/.ssh/known_hosts` file.
Finally, the user is asked for the password to log on to the server.

![Server Authentication
procedure<span label="fig:1"></span>](RHCSA/Mod1/chapters/1.5.a%20Server%20auth)

## Client authentication without password

The client can also prove it’s identity without a password by the use of
a public key that it provides to the server. The private key of the user
is stored in the home directory of the user `~/.ssh/id_rsa`. A packet
encrypted with the private key is sent to the server which knows the
user’s public key. Some complex calculations based on this is performed
on the authentication token sent from the client and if the identity is
confirmed, then the user is logged in without needing a password.

![Public Key Client Authentication without
password.<span label="fig:1"></span>](RHCSA/Mod1/chapters/1.5.b%20Client%20auth%20with%20no%20password)
