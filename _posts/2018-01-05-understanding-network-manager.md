---
layout : post
title : 'Understanding Network Manager'
tags : [RHCSA, RHCSA_mod, Configuring Networking, Understanding Network Manager]
categories: [RHCSA]
---


The Network Manager is the part of the OS that manages the NIC. There
are three different ways of changing the network configuration on the
NIC: using the `ip` command, the `nmcli` command and the Network Manager
TUI (Text User Interface).

``` console
# ip addr add dev ens33 10.0.0.10/24
# nmcli con add con-name ens33 ifname ens33 type ethernet ip4 10.0.0.13/24
```

When the `ip addr add` command is used, the ip address is added directly
to the physical NIC, which can start using it immediately. However, this
data is impersistent since the information is not managed by any
service. So, a reboot or even a simple bringing down of the interface
erases the data. This is why the information needs to be stored in
`/etc/sysconfig/network-scripts/ifcfg-ens33`.

So, when either the `nmcli` or the Network Manager TUI is used to
configure the network settings, the Network Manager service ensures the
data is stored in the above file and is thus available after every boot
or interface restart.

The `ip` command is used for temporary changes only, while the Network
Manager is used for persistent changes.
