---
layout : post
title : 'Understanding FTP Configuration'
tags : [RHCSA, RHCSA_mod, Configuring FTP Services, Understanding FTP Configuration]
categories: [RHCSA]
---


On RHEL 7, **vsftpd** is the default FTP server. The configuration files
for it are stored in `/etc/vsftpd` directory. The prime among these is
the `/etc/fsftpd/vsftpd.conf`.

## Types of FTP users

There can be both anonymous users and authenticated users connecting to
a FTP server. Anonymous users can access the FTP site without any
rights, and yet needs to access files anyway. The document root for the
FTP server is `/var/ftp`. It also happens to be the home directory of
the system user *ftp*. When an anonymous user accesses the FTP server,
he gets access to the home directory of the *ftp* user. However, the
directory itself is owned by the user *root* and belongs to the group
*root*. Others only have read-execute(r-x) permissions on that
directory. By default, the access rights are generally configured
correctly.
