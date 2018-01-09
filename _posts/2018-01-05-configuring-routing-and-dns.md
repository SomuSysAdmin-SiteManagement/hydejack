---
layout : post
title : 'Configuring Routing and DNS'
tags : [RHCSA, RHCSA_mod, Configuring Networking, Configuring Routing and DNS]
categories: [RHCSA]
---


On a temporary basis, the `ip route add` command can add a new default
route. The settings can be shown using `ip route show`. However, to make
the settings permanent, we need to edit the file
`/etc/sysconfig/network-scripts/ifcfg-ens33` (where *ens33* is the name
of our interface). Merely changing the value of the Gateway will
suffice. After changing the value of the gateway, we need to bring the
interface down and up again\!

``` console
# nmcli con down ens33; nmcli con up ens33
Connection 'ens33' successfully deactivated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/3)
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/5)
```

Likewise, the values for DNS Server(s) are also specified in the
`ifcfg-ens33` file. There can be multiple DNS servers, where the
successive server(s) are contacted (in order) only if the preceding ones
were down (or couldn’t be contacted).
