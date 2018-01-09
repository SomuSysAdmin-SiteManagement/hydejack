---
layout : post
title : 'Understanding Routing and DNS'
tags : [RHCSA, RHCSA_mod, Configuring Networking, Understanding Routing and DNS]
categories: [RHCSA]
---


## Default route

To connect to another network (or the internet), the server needs to
know an IP Address of another computer that can connect it to the
desired network. This is called the **default route**. It must be
present on the same network as the host.

If we consider in the diagram that the Computer with the IP address
*192.168.1.19* is our host that needs a packet to reach another computer
on the internet, then the default route for it would be *192.68.1.1* as
it is the computer through which our server is connected to another
network. Further, to pass along the packet to the receiver on the
internet, the computer with IP *192.168.1.1* will have to go through
another computer’s IP as it’s own default route that can connect it to
the internet. Here, that is *10.0.0.1*.

![Default
Route<span label="fig:1"></span>](RHCSA/Mod1/chapters/1.9.a%20Default%20Route)

Let us consider another scenario where the host *1*0.0.0.20 wants to
send a packet to *192.168.1.19*. Its default route will have to through
*192.168.1.1*.

Now, consider *10.0.0.20* needs to send a packet back to *192.168.1.19*,
but also needs to send packets to the internet. Then, the default route
would be the IP address of the computer that can connect us to the
internet (*10.0.0.1*). However, to eventually reach *192.168.1.19*, the
packets need a way to reach *192.168.1.1* first, given by *10.0.0.10*.
Thus, sometimes a computer needs to be configured for multiple routers.

## DNS

A Domain Name System (DNS) server stores the domain names along with a
list of their corresponding IP addresses, and when packets destined for
a certain domain name are available, it provides the actual IP addresses
for it. It translates the domain name to an IP address.
