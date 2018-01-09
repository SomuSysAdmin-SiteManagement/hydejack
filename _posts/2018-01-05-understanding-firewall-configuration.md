---
layout : post
title : 'Understanding Firewall Configuration'
tags : [RHCSA, RHCSA_mod, Configuring a Firewall, Understanding Firewall Configuration]
categories: [RHCSA]
---


The Linux kernel has a firewalling functionality called **netfilter**.
In previous versions of RHEL, it used to be managed with **iptables**.
However, now the default management interface is **firewalld** (even
though iptables can still be used).

The design purpose of firewalld was to make firewall configuration easy,
and this has been achieved with interfaces. Each of these interfaces is
assigned a zone. There can be a private zone for private messages, where
nothing is filtered, or a public zone for a server directly connected to
the internet.

Next, services have to be connected to zones. Many services are already
available by default and those that aren’t are easy to configure and
connect to the appropriate zone. Once these services are configured and
are available, there are only a couple of command line utilities that we
can use to setup our firewall.
