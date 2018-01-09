---
layout : post
title : 'Understanding NIC Naming'
tags : [RHCSA, RHCSA_mod, Configuring Networking, Understanding NIC Naming]
categories: [RHCSA]
---


A Network Interface Card (NIC) is the physical hardware connecting the
host machine to the network. In older versions of RHEL, the naming
convention was simpler, with the Ethernet interface named simply as
*eth0*. In RHEL 7, there are 3 different naming schemes available.

## Network Device Naming Schemes

<span>M<span>0.15</span>|M<span>0.4</span>|M<span>0.34</span></span>
**Scheme** &**Description** &**Example**  
**BIOS Naming** \&Based on Hardware properties of the Network card. &

|                   |                    |
| :---------------- | :----------------- |
| **em\[\(1-N\)\]** | Embedded NICs      |
| **p6p5**          | PCI Slot-6, Port-1 |

  
**Udev Naming** \&Classical Naming Scheme with Interface number.
&**Eth\(N\)** - example: eth0, eth1...  
**Physical Naming** \&Same as BIOS Naming &  
**Logical Naming** &.\<vlan\> and :\<alias\> &  

The entire purpose to the NIC naming scheme is to make it easier to
identify the hardware NIC associated with an interface for cases where
multiple NICs are available at a server.
