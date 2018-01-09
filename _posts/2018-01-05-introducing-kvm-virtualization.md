---
layout : post
title : 'Introducing KVM Virtualization'
tags : [RHCSA, RHCSA_mod, Working with Virtual Machines, Introducing KVM Virtualization]
categories: [RHCSA]
---


There are some basic requirements for KVM Virtualization on a server:

## CPU Virtualization Support

The CPU needs to be capable to support virtualization. This can be
easily verified using the command:

``` console
# grep -E "vmx|svm" /proc/cpuinfo
```

The presence of the `vmx` flag is the indication that the CPU supports
Intel’s VT-x virtualization. However, for AMD processors, the equivalent
is `svm` indicating the presence of AMD’s SVM technology.

If the processor does in fact support virtualization, then the Linux
Kernel can load the `kvm` and the `kvm-intel` or `kvm-amd` modules. On
top of the kernel lies the **libvirtd** daemon, which allow us to manage
the KVM virtualization. It can also communicate other virtualization as
well, such as Linux
containers.

![Virtualization<span label="fig:2 Virtualization"></span>](RHCSA/Mod2/chapters/2.12.a)

`libvirtd` only serves as a generic interface to virtualization that can
be used by other management programs such as **virt-manager**, which is
a GUI based VM management tool. There’s also **virsh**, a shell-based VM
manager, that is extremely useful to manage multiple VMs in an automated
way\!

There is also `vert-install`, a small installation interface that allow
us to install VMs.
