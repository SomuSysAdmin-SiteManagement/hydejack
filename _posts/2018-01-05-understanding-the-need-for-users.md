---
layout : post
title : 'Understanding the need for Users'
tags : [RHCSA, RHCSA_mod, Managing Users and Groups, Understanding the need for Users]
categories: [RHCSA]
---


User accounts are not just to ensure that different people use resources
with accountability and resource management. Several processes also have
to execute with permissions given to them by their respective user
accounts.

For example, the `apache` web server’s processes and services execute
under the permissions given to the `apache` account. This account
doesn’t have root privileges, which ensures that in case of security
breaches of the apache user account, the culprit doesn’t gain access to
any critical resources that only an administrator or the root account
should have access to.
