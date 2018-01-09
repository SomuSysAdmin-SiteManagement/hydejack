---
layout : post
title : 'Using Network Analysis Tools'
tags : [RHCSA, RHCSA_mod, Configuring Networking, Understanding Network Analysis Tools]
categories: [RHCSA]
---


For troubleshooting network issues, we first check our own network
information. We use `ip addr show` to ensure our IP address is correct.
Then, we use `ip route show` to ensure that the default route is set to
an IP that’s in the same IP network as (one of) our own IP address. Next
we check the DNS name resolution by printing `/etc/resolv.conf`.

``` console
# ip addr show
# ip route show
# cat /etc/resolv.conf
```

## ping

If the problem still persists, further testing is required. First we
ping the nearest router, then the one after that till we can reach the
internet, unless there’s an error, in which case we can know exactly
which network has a
problem.

``` console
# ping -c 1 192.168.0.1 # Pinging the default router by sending 1 packet.
```

### ping flood test

This is a bandwidth test, instead of a connectivity test and tells us
how many packets are being dropped on real time.

``` console
# ping -f cliServer
PING cliServer.somuVMnet.local (90.0.18.206) 56(84) bytes of data.
.^
--- cliServer.somuVMnet.local ping statistics ---
5215 packets transmitted, 5215 received, 0% packet loss, time 3354ms
rtt min/avg/max/mdev = 0.140/0.381/6.757/0.516 ms, ipg/ewma 0.643/0.287 ms
```

It prints a `.` for every ECHO\_REQUEST and prints a backspace for every
reply. Thus, the frequency of appearance of `.`s on the screen
represents the frequency of packet loss. Further, since an interval is
not given, it sends the packets as soon as a reply is received, thus
giving us a measure of the bandwidth of the line in use. NOTE that
superuser privileges are required to flood ping without an interval.

## traceroute

En-route to the destination server, the time it took to reach every
router and the time taken is displayed by this command. If there is some
kind of filtering enabled on the server, then the data is redacted with
`*`s.

``` console
# traceroute 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
1  amontpellier-653-1-352-1.w90-0.abo.wanadoo.fr (90.0.16.1)  4.277 ms  4.078 ms  3.954 ms
2  202.38.180.121 (202.38.180.121)  7.041 ms  6.943 ms  6.890 ms
3  10.10.50.1 (10.10.50.1)  6.796 ms  6.565 ms  6.614 ms
4  202.38.180.50 (202.38.180.50)  12.069 ms  11.962 ms  11.829 ms
5  108.170.253.97 (108.170.253.97)  18.954 ms  18.886 ms 108.170.253.113 (108.170.253.113)  18.768 ms
6  209.85.240.133 (209.85.240.133)  18.232 ms 209.85.240.159 (209.85.240.159)  12.603 ms 72.14.239.7 (72.14.239.7)  12.381 ms
7  google-public-dns-a.google.com (8.8.8.8)  12.223 ms  15.113 ms  14.561 ms
```

## host

The host command returns the IP address of any domain name that the
machine can resolve with the host-name resolution process. This means a
result will be produced even if the host is merely added in the
`/etc/hosts` file and not configured on the DNS server.

``` console
# host www.somusysadmin.com
www.somusysadmin.com has address 104.27.137.245
www.somusysadmin.com has address 104.27.136.245
www.somusysadmin.com has IPv6 address 2400:cb00:2048:1::681b:89f5
www.somusysadmin.com has IPv6 address 2400:cb00:2048:1::681b:88f5
```

## dig

**dig** gives more in-depth information about the name lookup process
through DNS. This means no information is provided if the destination
machine is unknown to the DNS server.

``` console
# dig www.somusysadmin.com

; <<>> DiG 9.9.4-RedHat-9.9.4-51.el7 <<>> www.somusysadmin.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 65365
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 13, ADDITIONAL: 12

;; QUESTION SECTION:
;www.somusysadmin.com.      IN  A

;; ANSWER SECTION:
www.somusysadmin.com.   199 IN  A   104.27.136.245
www.somusysadmin.com.   199 IN  A   104.27.137.245

;; AUTHORITY SECTION:
com.            100319  IN  NS  a.gtld-servers.net.
com.            100319  IN  NS  j.gtld-servers.net.
com.            100319  IN  NS  c.gtld-servers.net.
com.            100319  IN  NS  m.gtld-servers.net.
com.            100319  IN  NS  l.gtld-servers.net.
com.            100319  IN  NS  h.gtld-servers.net.
com.            100319  IN  NS  b.gtld-servers.net.
com.            100319  IN  NS  d.gtld-servers.net.
com.            100319  IN  NS  i.gtld-servers.net.
com.            100319  IN  NS  g.gtld-servers.net.
com.            100319  IN  NS  k.gtld-servers.net.
com.            100319  IN  NS  f.gtld-servers.net.
com.            100319  IN  NS  e.gtld-servers.net.

;; ADDITIONAL SECTION:
a.gtld-servers.net. 34680   IN  A   192.5.6.30
j.gtld-servers.net. 97045   IN  A   192.48.79.30
c.gtld-servers.net. 88438   IN  A   192.26.92.30
m.gtld-servers.net. 88436   IN  A   192.55.83.30
l.gtld-servers.net. 32537   IN  A   192.41.162.30
h.gtld-servers.net. 88436   IN  A   192.54.112.30
b.gtld-servers.net. 88438   IN  A   192.33.14.30
d.gtld-servers.net. 48310   IN  A   192.31.80.30
i.gtld-servers.net. 99400   IN  A   192.43.172.30
g.gtld-servers.net. 88436   IN  A   192.42.93.30
f.gtld-servers.net. 143261  IN  A   192.35.51.30
e.gtld-servers.net. 138671  IN  A   192.12.94.30

;; Query time: 766 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Wed Nov 29 11:59:42 IST 2017
;; MSG SIZE  rcvd: 486
```

The status of `NOERROR` indicates the operation was successful. The
question section containing `www.somusysadmin.com. IN A` indicates that
an address for `www.somusysadmin.com` was queried and the `A` indicates
an address was asked for.

The answer section is provided with the different IP Addresses for the
domain name. At the bottom, the server that was queried and operation
details are noted.

`SERVER: 8.8.8.8#53(8.8.8.8)`.

To perform a bit of performance optimization, we can add our own name
server before a public DNS if we want to directly fetch the data. To do
this, simply add a new DNS in
`/etc/sysconfig/network-scripts/ifcfg-ens33`. After this, a restart of
the NetworkManager is required for the new configuration settings to
take effect. f

``` console
# systemctl restart NetworkManager
```

When we try to dig a site that doesn’t exist, the output is :

``` console
# dig siteDoesntExist.com

; <<>> DiG 9.9.4-RedHat-9.9.4-51.el7 <<>> siteDoesntExist.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 22389
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;sitedoesntexist.com.       IN  A

;; Query time: 2 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Wed Nov 29 12:27:15 IST 2017
;; MSG SIZE  rcvd: 37
```

The `NXDOMAIN` status is indicative of the fact that the domain is
non-existent.

## Physical network problems

Sometimes there may be a physical problem in the network and not a
problem with the configuration. In that case, the `ip -s link` command
can give us a hint about the statistics of the interface and thus show
us if packets are being dropped, etc.

``` console
# ip -s link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT qlen 1
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
RX: bytes  packets  errors  dropped overrun mcast
126271     1008     0       0       0       0
TX: bytes  packets  errors  dropped carrier collsns
126271     1008     0       0       0       0
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
link/ether 00:0c:29:d6:73:d0 brd ff:ff:ff:ff:ff:ff
RX: bytes  packets  errors  dropped overrun mcast
22610122   182819   0       0       0       0
TX: bytes  packets  errors  dropped carrier collsns
1686685    16970    0       0       0       0
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT qlen 1000
link/ether 52:54:00:a5:7f:97 brd ff:ff:ff:ff:ff:ff
RX: bytes  packets  errors  dropped overrun mcast
0          0        0       0       0       0
TX: bytes  packets  errors  dropped carrier collsns
0          0        0       0       0       0
4: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0 state DOWN mode DEFAULT qlen 1000
link/ether 52:54:00:a5:7f:97 brd ff:ff:ff:ff:ff:ff
RX: bytes  packets  errors  dropped overrun mcast
0          0        0       0       0       0
TX: bytes  packets  errors  dropped carrier collsns
0          0        0       0       0       0
```

