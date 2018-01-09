---
layout : post
title : 'Creating a Kickstart file'
tags : [RHCSA, RHCSA_mod, Using Kickstart, Creating a Kickstart file]
categories: [RHCSA]
---


If the goal is to install just one server, it is easier to just install
it manually, but for a number of servers (with the same configuration),
using kickstart is much better. The kickstart file is passed to the
installer.

The home directory or user `root` contains two kickstart files:

``` console
# ls -l /root
total 8
-rw-------. 1 root root 2183 Nov 25 09:09 anaconda-ks.cfg
-rw-r--r--. 1 root root 2214 Nov 25 09:47 initial-setup-ks.cfg
```

To use kickstart with only minor variaitons of the given configuraiton
in either of those files, we could make a copy of the file and edit the
attributes till they meet our requirements. However, a custom
installation by creating a new kickstar file is also possible. For this,
we need the `system-config-kickstart` utility (isn’t installed by
default).

The `system-config-kickstart` command launches the GUI tool that creates
the required kickstart files. This GUI has the ability to ask for all
the options asked during boot. However, some essential options like the
option to create LVM is missing from the kickstart configuration. When
done, the options can be saved to the file system.

## Installation Scripts

The real power of kickstart comes from the fact that it is capable of
running pre-installation and post-installatin scripts.
