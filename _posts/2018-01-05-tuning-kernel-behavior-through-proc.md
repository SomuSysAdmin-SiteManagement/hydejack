---
layout : post
title : 'Tuning kernel behavior through proc'
tags : [RHCSA, RHCSA_mod, Managing the Kernel, Tuning kernel behavior through proc]
categories: [RHCSA]
---


The Linux kernel provides an easy to use interface to optimize kernel
parameters, called **proc**. To use it, we have to go to the `/proc`
file system. In there we can find current status information about the
kernel, and also a `/proc/sys` directory that helps us optimize kernel
parameters.

There are several files that convey kernel status information, such as
the `/proc/cpuinfo` file that helped us detect if the virtualization
flag (*vmx*) was set for the processor. There is also the
`/proc/partitions` file that shows us the kernel partition table
contents. The `/proc/meminfo` file gives us detailed information about
the memory.

In the `/proc/sys` directory, there is a subdirectory for every
interface offered by the kernel. Some of the most important ones are :
**kernel** - to optimize core kernel functionality, **net** - for
networking and **vm** - concerning virtual memory management.

If the module has to be loaded, then the parameters should be edited
with `/etc/modprobe.d/<moduleName>.conf`. However, if they’re a part of
the main kernel, then there’s a chance that there’s a `sysctl` setting
available for it. In that case, it can be edited via the proc file
system. All sysctl settings are available under `/proc/sys` directory.

If we go to the `/proc/sys/kernel` folder, we can see that there are a
lot of parameters that the kernel is using. While some of the parameters
are quite advanced, some are simple and provide useful information such
as the *osrelease and hostname* files.

``` console
# cat /proc/sys/kernel/osrelease
3.10.0-693.11.1.el7.x86_64
# cat /proc/sys/kernel/hostname
vmPrime.somuVMnet.local
```

We can also change the parameters here. For example, in the
`/proc/sys/net/ipv4` directory, there is a file for a parameter called
*ip\_forward*.

``` console
# cat ip_forward
1
```

This means that the system is configured to forward packets, i.e., the
system is configured as a router. If we want to change this behavior,
and want to turn that parameter off, we just use the command `echo 0 >
ip\_forward`. Note that changing parameters this way is not persistent.

In the `/proc/sys/vm` directory, there is a file for the swappiness
parameter, i.e., the willingness to swap or store a file on the hard
disk from the RAM when it’s no longer needed. This parameter accepts
values between \(0\) to \(100\) and if we increase the swappiness, we
make the kernel swap out data to disk faster.

An important point of note is that the system parameters in this
directory are really advanced, and we should only change the values here
if we know exactly what we’re doing. This is not a place for
*experimentation*, as it may even cause boot to fail.
