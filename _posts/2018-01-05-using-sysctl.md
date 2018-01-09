---
layout : post
title : 'Using sysctl'
tags : [RHCSA, RHCSA_mod, Managing the Kernel, Using sysctl]
categories: [RHCSA]
---


To make the changes that we’ve made persistent, we have to tune
**sysctl**.

## sysctl command

The `sysctl` command is used to configure the kernel parameters at
runtime. The `sysctl -a` command gives us a list of all the tunable
options that are currently set.

``` console
# sysctl -a
abi.vsyscall32 = 1
crypto.fips_enabled = 0
debug.exception-trace = 1
debug.kprobes-optimization = 1
debug.panic_on_rcu_stall = 0
dev.cdrom.autoclose = 1
dev.cdrom.autoeject = 0
...
vm.vfs_cache_pressure = 100
vm.zone_reclaim_mode = 0
```

This list is also greppable, and thus, we can easily find the parameter
we’re looking for.

``` console
# sysctl -a | grep ip_forward
sysctl: reading key "net.ipv6.conf.all.stable_secret"
sysctl: reading key "net.ipv6.conf.default.stable_secret"
sysctl: reading key "net.ipv6.conf.ens33.stable_secret"
sysctl: reading key "net.ipv6.conf.lo.stable_secret"
sysctl: reading key "net.ipv6.conf.virbr0.stable_secret"
sysctl: reading key "net.ipv6.conf.virbr0-nic.stable_secret"
net.ipv4.ip_forward = 0
net.ipv4.ip_forward_use_pmtu = 0
```

The names of the parameters shown here correspond to a file of the same
name in the `/proc/sys/` directory. Thus, the `net.ipv4.ip_forward`
parameter can be set using the file `/proc/sys/net/ipv4/ip_forward`
file.

Upon booting the sysctl process reads a bunch of configuration files to
appropriately set the kernel parameters. In earlier versions, there used
to be only one config file: `/etc/sysctl.conf`, but on RHEL 7 it’s
empty. For custom settings, the `/etc/sysctl.d/<name>.conf` files should
be used. The default sysctl config is loaded from the
`/usr/lib/sysctl.d/` files. The contents of that directory is:

``` console
# ls /usr/lib/sysctl.d/
00-system.conf  10-default-yama-scope.conf  50-default.conf  60-libvirtd.conf
```

The numbers in front of the file names just ensure that the config
settings are read from the files in order. So, *00-system.conf* will be
read before *60-libvirtd.conf*. This gives a cascading effect, and if a
setting is saved in multiple files then the last file to be read
contains the value that’ll be used.

Now, if we want our computer to be able to forward IP packets like a
router, we edit the file `/etc/sysctl.d/50-ipforward.conf`:

``` bash
net.ipv4.ip_forward = 1
```

After the next reboot, the system will be able to route ip packets.
There are certain tools that can write directly to the sysctl config
files, but they should be avoided, since to verify that these work, we
have to echo their values from the `/proc` file system. Then all we need
to do is restart the system, and since the tunable is a part of sysctl
system, the parameters will be applied at next boot.
