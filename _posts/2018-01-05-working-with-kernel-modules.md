---
layout : post
title : 'Working with Kernel Modules'
tags : [RHCSA, RHCSA_mod, Managing the Kernel, Working with Kernel Modules]
categories: [RHCSA]
---


The linux kernel works with modules to only load those modules which
provided a required functionality. Thus, these kernels are very lean
since only the functionalities being used are kept.

## Viewling loaded Kernel Modules

The `lsmod` command shows us all the kernel module that have been
loaded.

``` console
# lsmod
Module                  Size  Used by
nls_utf8               12557  1
isofs                  39844  1
fuse                   91874  3
xt_CHECKSUM            12549  1
ipt_MASQUERADE         12678  3
...
dm_region_hash         20813  1 dm_mirror
dm_log                 18411  2 dm_region_hash,dm_mirror
dm_mod                123303  14 dm_log,dm_mirror
```

The provided data is the name and size of the module followed by the
number of programs currently using the module. In older versions of
linux it was required to manually load modules to access certain
functionalities. Modern linux distros don’t require this. The **udev**
process takes care of loading modules automatically. The activity of
this process can be monitored using the `udevadm monitor` command.

``` console
monitor will print the received events for:
UDEV - the event which udev sends out after rule processing
KERNEL - the kernel uevent
```

Now if we were to attach a pen drive (and then remove it) then that’d
trigger the
following:

``` bash
KERNEL[15445.778458] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1 (usb)
KERNEL[15445.786957] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0 (usb)
UDEV  [15445.830980] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1 (usb)
KERNEL[15445.851667] add      /module/usb_storage (module)
UDEV  [15445.877116] add      /module/usb_storage (module)
...

KERNEL[15893.875998] add      /module/fat (module)
KERNEL[15893.876022] add      /kernel/slab/fat_cache (slab)
KERNEL[15893.876030] add      /kernel/slab/fat_inode_cache (slab)
KERNEL[15893.876037] add      /module/vfat (module)
UDEV  [15893.878942] add      /module/fat (module)
UDEV  [15893.878962] add      /kernel/slab/fat_cache (slab)
UDEV  [15893.878969] add      /kernel/slab/fat_inode_cache (slab)
UDEV  [15893.879002] add      /module/vfat (module)

...
UDEV  [15462.218411] remove   /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1 (usb)
KERNEL[15462.232117] remove   /host3/target3:0:0 (scsi)
UDEV  [15462.232957] remove   /host3/target3:0:0 (scsi)
```

The moment new hardware is detected, it generates an event which is then
received by udev to respond appropriately. The insertion of the pen
drive (usb key) into the usb slot triggered the udev process to load the
*fat* and *vfat* modules \[Lines `8-15`\].

However, when the USB key is detached, the modules aren’t unloaded. This
can be verified by the presence of both *fat & vfat* modules in the
output of:

``` console
# lsmod | grep fat
vfat                   17461  0
fat                    65950  1 vfat
```

## Modprobe

The `modprobe` command is used to load kernel modules manually. With the
`-r` option, the command `modprobe -r` can also be used to unload kernel
modules. We can remove the vfat module by:

``` console
# modprobe -r vfat
# lsmod | grep fat
#
```

Note that the *fat* kernel module was loaded while loading the *vfat*
kernel module and when the *vfat* module was unloaded by `modprobe`,
it’s dependency, *fat* module was also unloaded. Thus, `modprobe` also
manages loading and unloading the dependencies. To load the *vfat*
module again, we use:

``` console
# modprobe vfat
# lsmod | grep fat
vfat                   17461  0
fat                    65950  1 vfat
```

Normally, this is something we have to rarely do, since the udev process
does its work so well once the kernel has detected the hardware.
However, this is extremely useful for situations where the kernel module
has been edited/updated manually and needs to be reloaded.
