---
layout : post
title : 'Understanding semanage fcontext and chcon differences'
tags : [RHCSA, RHCSA_mod, Managing SELinux, Understanding semanage fcontext and chcon differences]
categories: [RHCSA]
---


In certain man page entries, we might come across the command `chcon`,
which is a *bad* program, and shouldn’t be used. For this, we need to
understand the difference between `semanage`, `fcontext` and `chcon`.

Let us consider a scenario where we need to change the context of a file
`/blah/index.html`. Suppose we want to set its context to
*httpd\_sys\_content\_t*. To do this using `chcon`, we would need to use
the command:

``` console
# chcon -R --type=httpd_sys_content_t /blah
```

What this command does is set the given context type to the inode, i.e.,
applies the change to the file system. The corresponding entry for it in
the policy still remains *default\_t*. This is bad because whenever a
relabel operation occurs (typically on the entire root file system
\[relabel of `/`\]), the context for the `/blah` directory would be
overwritten to *default\_t*, because during a relabel everything in the
policy overwrites everything in the file system. Thus, it is absolutely
critical that SELinux information is always written to the policy
first\! This is why to set the context of a file/directory, we use:

``` console
# semanage fcontext -a -t httpd_sys_content_t "/blah(/.*)?"
```

This sets the context in the policy and thus the change will survive the
relabel activity.
