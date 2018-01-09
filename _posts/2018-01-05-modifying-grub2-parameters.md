---
layout : post
title : 'Modifying Grub2 Parameters'
tags : [RHCSA, RHCSA_mod, Managing and Understanding the Boot Procedure, Modifying Grub2 Parameters]
categories: [RHCSA]
---


The primary grub configuration file is `/etc/default/grub`. The default
contents of it look like:

``` bash
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet"
GRUB_DISABLE_RECOVERY="true"
```

The `GRUB_TIMEOUT` parameter defines the amount of time the system waits
in the Grub boot menu for user input. The most important parameter is
`GRUB_CMDLINE_LINUX` which defines which arguments are passed on to the
linux kernel when the system is booting. The last portion of this
parameter, `rhgb quiet` stops Grub from showing us what it’s doing
during boot. To enable this feature, we need to delete those arguments.

Next, we take a look at the `/etc/grub.d` directory. The files in here
are shell scripts that aren’t meant to be changed normally, and help
with the boot process.

After making all changes, we need to execute the `grub2-mkconfig`
command to update the changes to the Grub2 boot-loader. The command
reads (and compiles) every grub related config file. This generates a
grub configuration file (to send to the boot loader).

To see all these changes, we need to reboot our computer. To verify that
the changes have been applied correctly, we can enter the Grub menu
using the *Escape* key. In there, we can find the kernel line with all
the options that are used. A *CTRL+X* at this point causes a reboot with
the new parameters passed to the kernel.
