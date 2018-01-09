---
layout : post
title : 'Understanding Group Membership'
tags : [RHCSA, RHCSA_mod, Managing Users and Groups, Understanding Group Membership]
categories: [RHCSA]
---


Groups are especially useful to enable users to share files with one
another. These groups may be additional groups known as secondary
groups.

The `/etc/passwd` file doesn’t contain any reference to the secondary
groups that the user is a part of, even though the `/etc/group` file
lists that user as a member. Thus, the best way to obtain the groups the
user is a part of is by using the `id` command.|

``` console
$ id lisa
uid=1002(lisa) gid=1002(lisa) groups=1002(lisa), 5009(sales)
```
