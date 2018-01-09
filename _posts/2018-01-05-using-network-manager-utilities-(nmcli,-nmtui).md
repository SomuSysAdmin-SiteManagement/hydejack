---
layout : post
title : 'Using Network Manager utilities (nmcli, nmtui)'
tags : [RHCSA, RHCSA_mod, Configuring Networking, Using Network Manager utilities (nmcli, nmtui)]
categories: [RHCSA]
---


The `nmcli` tool controls the Network Manager from the command line.
Just like the `ip` command, its syntax is `nmcli <options> <object>
<command>`. The objects are like subcommands as in the case of `ip`
command. The most important object is the **connection(c)**.

A network interface is just an interface with some connection associated
with it. A connection is however, an abstract layer lying on top of the
interface. The concept is that every Network interface has a default
connection, but we can add a testing connection as well. We can then
switch between these connections using the network manager utilities.

## nmcli

### nmcli connection show

``` console
$ nmcli connection show
NAME   UUID                                  TYPE            DEVICE
ens33  26c54678-6784-47ba-8afc-ca9924ed63af  802-3-ethernet  ens33
```

The command to add a new connection is: (line 9)

``` console
# ls /etc/sysconfig/network-scripts/
ifcfg-ens33  ifdown-eth   ifdown-post    ifdown-Team      ifup-aliases  ifup-ipv6   ifup-post    ifup-Team      init.ipv6-global
ifcfg-lo     ifdown-ippp  ifdown-ppp     ifdown-TeamPort  ifup-bnep     ifup-isdn   ifup-ppp     ifup-TeamPort  network-functions
ifdown       ifdown-ipv6  ifdown-routes  ifdown-tunnel    ifup-eth      ifup-plip   ifup-routes  ifup-tunnel    network-functions-ipv6
ifdown-bnep  ifdown-isdn  ifdown-sit     ifup             ifup-ippp     ifup-plusb  ifup-sit     ifup-wireless
# nmcli connection show
NAME   UUID                                  TYPE            DEVICE
ens33  26c54678-6784-47ba-8afc-ca9924ed63af  802-3-ethernet  ens33
# nmcli con add con-name testing ifname ens33 type ethernet ip4 10.0.0.15/24
Connection 'testing' (8bc5959e-3d1e-4738-8fa6-b584e4ba4388) successfully added.
# nmcli connection show
NAME     UUID                                  TYPE            DEVICE
ens33    26c54678-6784-47ba-8afc-ca9924ed63af  802-3-ethernet  ens33
testing  8bc5959e-3d1e-4738-8fa6-b584e4ba4388  802-3-ethernet  --
# ls /etc/sysconfig/network-scripts/
ifcfg-ens33    ifdown-bnep  ifdown-isdn    ifdown-sit       ifup          ifup-ippp  ifup-plusb   ifup-sit       ifup-wireless
ifcfg-lo       ifdown-eth   ifdown-post    ifdown-Team      ifup-aliases  ifup-ipv6  ifup-post    ifup-Team      init.ipv6-global
ifcfg-testing  ifdown-ippp  ifdown-ppp     ifdown-TeamPort  ifup-bnep     ifup-isdn  ifup-ppp     ifup-TeamPort  network-functions
ifdown         ifdown-ipv6  ifdown-routes  ifdown-tunnel    ifup-eth      ifup-plip  ifup-routes  ifup-tunnel    network-functions-ipv6
```

Note that upon creation of the new connection, the `nmcli` tool also
creates a config file called
`/etc/sysconfig/network-scripts/ifcfg-testing` (line 18) for the new
connection called *testing*.

### Switching Connections

The connection switching is as simple as bringing an interface down and
bringing an alternative up. We do this using `nmcli` by:

``` console
# nmcli con show
NAME     UUID                                  TYPE            DEVICE
ens33    26c54678-6784-47ba-8afc-ca9924ed63af  802-3-ethernet  ens33
testing  8bc5959e-3d1e-4738-8fa6-b584e4ba4388  802-3-ethernet  --
# nmcli con down ens33
Connection 'ens33' successfully deactivated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/11)
# nmcli con up testing
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/13)
# nmcli con show
NAME     UUID                                  TYPE            DEVICE
testing  8bc5959e-3d1e-4738-8fa6-b584e4ba4388  802-3-ethernet  ens33
ens33    26c54678-6784-47ba-8afc-ca9924ed63af  802-3-ethernet  --
```

## nmtui

This command provides a Text User Interface for the Network Manager.

On restarting the Network Manager, all the set connections become
simultaneously activated. To avoid this, we would have to bring the
connection down on restart either through the config files or the TUI.

``` console
# systemctl restart NetworkManager
# systemctl status -l NetworkManager
```

