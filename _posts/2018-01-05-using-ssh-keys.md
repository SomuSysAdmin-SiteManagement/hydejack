---
layout : post
title : 'Using SSH Keys'
tags : [RHCSA, RHCSA_mod, Connecting to a RHEL Server, Understanding SSH keys]
categories: [RHCSA]
---


SSH keys can be used to authenticate an user instead of a password. The
private-public key pair can be generated using the `ssh-keygen` utility.

``` console
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/somu/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/somu/.ssh/id_rsa.
Your public key has been saved in /home/somu/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:0C5uEHnAgJvzFEc0ulfL1YSygT/YF5UtL9lweTfPDAc somu@vmPrime.somusysadmin.com
The key's randomart image is:
+---[RSA 2048]----+
| ..==.   ooo .E. |
|. ..++o.oo+ + o.o|
| o.oo+++o..B . *o|
|+ ...===. o o   +|
| +. o +oS  .     |
|  .. o .         |
|      o          |
|     .           |
|                 |
+----[SHA256]-----+
```

## Copying SSH keys

The SSH keys generated on the client now have to be copied to the server
which requires authentication. This can be done using `ssh-copy-id`.

``` console
$ ssh-copy-id 192.168.152.129
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
somu@192.168.152.129's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh '192.168.152.129'"
and check to make sure that only the key(s) you wanted were added.

$ ssh 192.168.152.129
Last login: Wed Nov 15 11:59:03 2017 from 192.168.152.128
```

While previous versions of `ssh-copy-id` didn’t support specifying a
port number, RHEL 7 onwards this feature is supported.

``` console
$ ssh-copy-id -p 2022 sander@ldap.rhatcertification.com
```

In case the public key is not recognized, it is possible to specify the
public key using the `ssh-copy-id -i <publicKeyFile.pub>` flag.

## Copying files to a server securely using SSH

Files can be copied to a server using SSH connection using the `scp`
(secure copy) utility.

``` console
$ scp -P 22 names 192.168.152.129:~
names                  100%   28     8.1KB/s   00:00
```

**NOTE** that the directory on which the file has to be copied to on the
server, (in this case the   directory) has to be specified for the copy
to be successful. Otherwise, `scp` just creates a local copy of the file
with the name of the server as the filename.

Also, if the port has to be specified, the flag is `-P` which is in
capital unlike `ssh` and `ssh-copy-id`.
