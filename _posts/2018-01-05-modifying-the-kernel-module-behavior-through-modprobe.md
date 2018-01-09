---
layout : post
title : 'Modifying the Kernel module behavior through modprobe'
tags : [RHCSA, RHCSA_mod, Managing the Kernel, Modifying the Kernel module behavior through modprobe]
categories: [RHCSA]
---


The information about any specific kernel module can be obtained through
the `modinfo` command.

``` console
# modinfo e1000
filename:       /lib/modules/3.10.0-693.11.1.el7.x86_64/kernel/drivers/net/ethernet/intel/e1000/e1000.ko.xz
version:        7.3.21-k8-NAPI
license:        GPL
description:    Intel(R) PRO/1000 Network Driver
author:         Intel Corporation, <linux.nics@intel.com>
rhelversion:    7.4
srcversion:     9E0A112E5D47C996E7C4A58
alias:          pci:v00008086d00002E6Esv*sd*bc*sc*i*
...
alias:          pci:v00008086d00001001sv*sd*bc*sc*i*
alias:          pci:v00008086d00001000sv*sd*bc*sc*i*
depends:
intree:         Y
vermagic:       3.10.0-693.11.1.el7.x86_64 SMP mod_unload modversions
signer:         CentOS Linux kernel signing key
sig_key:        61:B8:E8:7B:84:11:84:F6:2F:80:D6:07:79:AB:69:2A:49:D8:3B:AF
sig_hashalgo:   sha256
parm:           TxDescriptors:Number of transmit descriptors (array of int)
parm:           RxDescriptors:Number of receive descriptors (array of int)
parm:           Speed:Speed setting (array of int)
parm:           Duplex:Duplex setting (array of int)
parm:           AutoNeg:Advertised auto-negotiation setting (array of int)
...
parm:           copybreak:Maximum size of packet that is copied to a new buffer on receive (uint)
parm:           debug:Debug level (0=none,...,16=all) (int)
```

What’s really useful for us is the *parm* information towards the end of
the kernel module. While not every kernel module has them, many kernel
module do provide the option to set parameters. Thus, the `modinfo`
command gives us a list of all the parameters that can be set for a
kernel module.

Now, we can see the information about the parameters that are available
for the *cdrom* module:

``` console
# modinfo cdrom
filename:       /lib/modules/3.10.0-693.11.1.el7.x86_64/kernel/drivers/cdrom/cdrom.ko.xz
license:        GPL
rhelversion:    7.4
srcversion:     BE3BD0D17D080229D55B173
depends:
intree:         Y
vermagic:       3.10.0-693.11.1.el7.x86_64 SMP mod_unload modversions
signer:         CentOS Linux kernel signing key
sig_key:        61:B8:E8:7B:84:11:84:F6:2F:80:D6:07:79:AB:69:2A:49:D8:3B:AF
sig_hashalgo:   sha256
parm:           debug:bool
parm:           autoclose:bool
parm:           autoeject:bool
parm:           lockdoor:bool
parm:           check_media_type:bool
parm:           mrw_format_restart:bool
```

An interesting example of a parameter is the *lockdoor* parameter, which
when enabled locks the cd tray when the device is mounted. In case of
these boolean variables, the value `0 = false; 1 = true`. If however, we
try to remove a kernel module that’s being used, we get the message:

``` console
# modprobe -r cdrom
modprobe: FATAL: Module cdrom is in use.
```

If the cdrom weren’t in use, the command to set the parameter (stop
locking the disk tray when the cdrom is mounted) would be:

``` console
# modprobe cdrom lockdoor=0
```

## Setting kernel module parameters on older Linux versions

There was only one entry point for setting the kernel module parameters
on older versions of Linux : `/etc/modprobe.conf`. However, RHEL 7
onwards, this is no longer the case. There are a couple of extra
locations for modifying the kernel module parameters.

The folder `/lib/modprobe.d` contains many configuration files. These
files contain the default settings for their respective kernel modules.
The files are dropped here by the RPMs during installation. Typically,
we should avoid any modification in this folder.

To modify the kernel parameters we should choose a different directory:
`/etc/modprobe.d`. There is an excellent manpage for the modprobe.d
directory, which contains the format for specifying the parameters:

``` bash
options modulename option...
```

Thus, to set the kernel parameter for the lockdoor on cdrom to false, we
need to make a file: `/etc/modprobe.d/cdrom.conf` which contains just
one line:

``` bash
options cdrom lockdoor=0
```

Since in our case, we can’t reload the kernel module since it’s in use,
the only way to ensure that it works is by rebooting the server with
`reboot now`. Finding out if it worked might be problematic on a live
system.

For certain modules, we can go to the `/sys/module` directory which
contains a sub-directory for every kernel module that’s currently
loaded. We would want to look for a file called `parameters` that sits
in the directory for that module name, and check to see what value is
set. However, the cdrom module has no such file.

Next, we can check the wih the `dmesg` command, after filtering it
appropriately. Then we filter out the irrelevant stuff with the `grep`
command. The grep command can be provided an option `-A` which is
followed immediately by the number of lines starting from the matching
line should be printed.

``` console
# dmesg | grep cdrom
[    2.516616] cdrom: Uniform CD-ROM driver Revision: 3.20
# dmesg | grep -A5 cdrom
[    2.516616] cdrom: Uniform CD-ROM driver Revision: 3.20
[    2.516931] sr 2:0:0:0: Attached scsi CD-ROM sr0
[    2.818361] e1000 0000:02:01.0 eth0: (PCI:66MHz:32-bit) 00:0c:29:d6:73:d0
[    2.818369] e1000 0000:02:01.0 eth0: Intel(R) PRO/1000 Network Connection
[    3.137847] random: crng init done
[    3.666093] SGI XFS with ACLs, security attributes, no debug enabled
```

