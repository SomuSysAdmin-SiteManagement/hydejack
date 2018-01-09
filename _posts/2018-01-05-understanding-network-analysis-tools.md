---
layout : post
title : 'Understanding Network Analysis Tools'
tags : [RHCSA, RHCSA_mod, Configuring Networking, Understanding Network Analysis Tools]
categories: [RHCSA]
---


The following are a few network analysis tools that help diagnose
problems with the network.

<span>rM<span>0.77</span></span> **Name** &**Description**  
**hostname** \&Shows current hostname and provides an option to change
it.  
**ping** \&Performs a connectivity test to know if another computer can
be reached.  
**traceroute** \&Provides specific information about the routing between
the host and a destination computer. *NOTE* that many routers are
configured nowadays to not display information about their operation,
and thus information from `traceroute` may be inaccurate.  
**dig** \&Shows DNS information and helps diagnose DNS related
problems.  
**nmap** \&Advanced and potentially dangerous tool to get information
about remote service availability. Since a portscan can be performed to
determine which services are provided by a server using it, this utility
is considered hostile by many NetAdmins.  
**netscan** &

<span>lM<span>0.95</span></span> **Command** &**Description**  
**netstat -i** \&Packet information for network cards.  
**netstat -tulpen** \&Information about listening ports on a server:  
&

|       |    |                      |
| ----: | :-: | :------------------- |
| **t** | \- | TCP                  |
| **u** | \- | UDP                  |
| **l** | \- | Listening            |
| **p** | \- | Process Information  |
| **e** | \- | Extended Information |
| **n** | \- | Names                |
