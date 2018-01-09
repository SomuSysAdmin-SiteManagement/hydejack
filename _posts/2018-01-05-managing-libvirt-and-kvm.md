---
layout : post
title : 'Managing Libvirt and KVM'
tags : [RHCSA, RHCSA_mod, Working with Virtual Machines, Managing Libvirt and KVM]
categories: [RHCSA]
---


First we need to verify that the required kernel modules for KVM are
available. This can be done with:

``` console
# lsmod | grep kvm
kvm_intel             200704  0
kvm                   585728  1 kvm_intel
irqbypass              16384  1 kvm
```

The `lsmod` command shows us the status of the Linux Kernel modules. The
`kvm` module is the generic KVM support module, while the `kvm-intel`
module provides platform specific support for KVM virtualization.

Finally, we check if the `libvirtd` daemon is up and running using:

``` console
# systemctl status libvirtd
libvirtd.service - Virtualization daemon
Loaded: loaded (/usr/lib/systemd/system/libvirtd.service; enabled; vendor pre
Active: active (running) since Sat 2017-12-02 16:08:39 IST; 2h 43min ago
Docs: man:libvirtd(8)
http://libvirt.org
Main PID: 945 (libvirtd)
Tasks: 18 (limit: 32768)
CGroup: /system.slice/libvirtd.service
945 /usr/sbin/libvirtd
1210 /usr/sbin/dnsmasq --conf-file=/var/lib/libvirt/dnsmasq/default
1211 /usr/sbin/dnsmasq --conf-file=/var/lib/libvirt/dnsmasq/default

Dec 02 16:08:55 lappyPrime dnsmasq[1210]: compile time options: IPv6 GNU-getopt
Dec 02 16:08:55 lappyPrime dnsmasq-dhcp[1210]: DHCP, IP range 192.168.124.2 -- 1
Dec 02 16:08:55 lappyPrime dnsmasq-dhcp[1210]: DHCP, sockets bound exclusively t
Dec 02 16:08:55 lappyPrime dnsmasq[1210]: no servers found in /etc/resolv.conf,
Dec 02 16:08:55 lappyPrime dnsmasq[1210]: read /etc/hosts - 2 addresses
Dec 02 16:08:55 lappyPrime dnsmasq[1210]: read /var/lib/libvirt/dnsmasq/default.
Dec 02 16:08:55 lappyPrime dnsmasq-dhcp[1210]: read /var/lib/libvirt/dnsmasq/def
Dec 02 16:09:37 lappyPrime dnsmasq[1210]: reading /etc/resolv.conf
Dec 02 16:09:37 lappyPrime dnsmasq[1210]: using nameserver 8.8.8.8#53
Dec 02 16:09:37 lappyPrime dnsmasq[1210]: using nameserver 202.38.180.7#53
```

Thus, this machine is completely ready for virtualization\! If we run
the `ip link show` command, we can also find a virtual bridge called
virbr0, which is provided courtesy of KVM. This network acts as if it’s
connected to a virtual switch and provides inter-VM bridged networking
support.

``` console
# ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp1s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN mode DEFAULT group default qlen 1000
link/ether 3c:52:82:b9:05:5f brd ff:ff:ff:ff:ff:ff
3: wlo1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DORMANT group default qlen 1000
link/ether 3c:95:09:de:4e:8d brd ff:ff:ff:ff:ff:ff
4: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default qlen 1000
link/ether 52:54:00:62:77:f7 brd ff:ff:ff:ff:ff:ff
5: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc fq_codel master virbr0 state DOWN mode DEFAULT group default qlen 1000
link/ether 52:54:00:62:77:f7 brd ff:ff:ff:ff:ff:ff
```

