---
layout : post
title : 'Updating the kernel'
tags : [RHCSA, RHCSA_mod, Managing the Kernel, Using sysctl]
categories: [RHCSA]
---


The command to update the kernel is:

``` console
# yum update kernel
```

If the kernel has already been downloaded, we could use either of the
commands below:

``` console
# yum localinstall <kernelName>.rpm
# rpm -Uvh <kernelName>.rpm
```

In any case, the old kernel is not overwritten during the update, and
thus it can still be booted, as both are added to grub.
