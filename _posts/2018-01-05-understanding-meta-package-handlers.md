---
layout : post
title : 'Understanding Meta Package Handlers'
tags : [RHCSA, RHCSA_mod, Managing Software, Understanding Meta Package Handlers]
categories: [RHCSA]
---


In the old days, the `rpm` command was used to install packages, and it
was incapable of resolving dependencies (i.e., auto-installing other
packages/programs that were needed to make a package work). The syntax
needed for rpm is : `rpm -ivh packageName.rpm`. (**i**=install,
**v**=verbose, **h**=show hashes about progress).

Nowadays, due to the `yum` package installer, this is no longer an
issue. It works with repositories, which are installation sources for a
bunch of packages, and the command works by downloading indexes for the
repositories. The `yum` meta-package handler needs only the rpm name to
install it.

``` console
# yum install blah.rpm
```

At this point the yum command searches the indexes for any dependencies.
If any are found, they’re downloaded from the repository as well, before
the original package is installed.
