---
layout : post
title : 'Managing NIC Configuration with ip Command'
tags : [RHCSA, RHCSA_mod, Configuring Networking, Managing NIC Configuration with ip Command]
categories: [RHCSA]
---


An important feature of the `ip` command is all data is lost with
interface is reset. However, it’s extremely useful as it allows us to
test certain settings on NICs. The syntax of the ip command is : `ip
<options> <object> <command>`

| **Object** | \- | **Description**                          |
| ---------: | :-: | :--------------------------------------- |
|   **link** | \- | Network status information               |
|   **addr** | \- | Set network addresses                    |
|  **route** | \- | Helps manage routing table on the system |

To get the help for any option and object, simply replace the command
with the text "help". For example, to get help about the `ip addr`
command, we need to type `ip addr help`.

## show commands

### ip link show

This command displays the device attributes, and can be followed by a
device name to only view the details for that device/interface. Can be
used to find the available interface names and the associated MAC
addresses.

``` console
$ ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT qlen 1
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
link/ether 00:0c:29:d6:73:d0 brd ff:ff:ff:ff:ff:ff
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT qlen 1000
link/ether 52:54:00:a5:7f:97 brd ff:ff:ff:ff:ff:ff
4: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0 state DOWN mode DEFAULT qlen 1000
link/ether 52:54:00:a5:7f:97 brd ff:ff:ff:ff:ff:ff
```

### ip addr show

`ip addr show` command shows us the current (network) address
information. Based on the interface name, we can find its specific
information only as well, in which case the command has to be followed
by the name of the interface.

``` console
$ ip addr show ens33
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
link/ether 00:0c:29:d6:73:d0 brd ff:ff:ff:ff:ff:ff
inet 90.0.16.117/21 brd 90.0.23.255 scope global dynamic ens33
valid_lft 3037sec preferred_lft 3037sec
inet6 fe80::2b85:fb69:3b97:ec5f/64 scope link
valid_lft forever preferred_lft forever
```

Note that the `inet` address is the IPv4 address whereas `inet6` refers
to the IPv6 address.

### ip route show

``` console
$ ip route show
default via 90.0.16.1 dev ens33 proto static metric 100
90.0.16.0/21 dev ens33 proto kernel scope link src 90.0.16.117 metric 100
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1
```

So, in this case, we have a default route which sends everything through
the ip address *90.0.16.1*.

## ip addr add

This command is used to add an IP address to a device. Note that while
adding a new IP address, the address must always be followed by the
Subnet Mask, as otherwise the default value of *42* is applied, which
doesn’t make sense.

``` console
# ip addr add dev ens33 10.0.0.10/24
# ip addr show ens33
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
link/ether 00:0c:29:75:5a:36 brd ff:ff:ff:ff:ff:ff
inet 90.0.18.206/21 brd 90.0.23.255 scope global dynamic ens33
valid_lft 3587sec preferred_lft 3587sec
inet 10.0.0.10/24 scope global ens33
valid_lft forever preferred_lft forever
inet6 fe80::ba08:1835:69e5:e9e9/64 scope link
valid_lft forever preferred_lft forever
```

This is one of the improvements of `ip` over `ifconfig` which was
incapable of setting/showing multiple IP Addresses for the same device.
`ifconfig` is obsolete. To see the network statistics (as shown in
ifconfig command), the command is :

``` console
# ip -s link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT qlen 1
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
RX: bytes  packets  errors  dropped overrun mcast
55874      218      0       0       0       0
TX: bytes  packets  errors  dropped carrier collsns
55874      218      0       0       0       0
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
link/ether 00:0c:29:75:5a:36 brd ff:ff:ff:ff:ff:ff
RX: bytes  packets  errors  dropped overrun mcast
15408095   113357   0       0       0       0
TX: bytes  packets  errors  dropped carrier collsns
1857459    13898    0       0       0       0
```

## ip route add

This command is used to add a new route.

``` console
# ip route add 20.0.0.0/8 via 192.168.4.4
```

However, all settings using the `ip` command are temporary and thus will
be reset with every reboot. To makes these settings permanent, we need
to provide this info in an appropriate configuration file to be loaded
during each boot.
