---
layout : post
title : 'sshd_config'
tags : [RHCSA, RHCSA_mod, Connecting to a RHEL Server, sshd_config]
categories: [RHCSA]
---


The details of the method of connection to a server is stored in the
**sshd\_config** file, located in `/etc/ssh/sshd_config`.

Some of the options
are:

|   **Option**    | **Description**                                            | **Default Value** |
| :-------------: | :--------------------------------------------------------- | :---------------: |
|      Port       | The port number which is used for SSH on that server       |        22         |
| PermitRootLogin | Whether an user is allowed to login as *root* user via SSH |        yes        |

If the root login on the server via SSH is disabled, it generally makes
the server a little bit more secure\!
