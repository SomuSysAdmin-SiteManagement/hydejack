---
layout : post
title : 'Understanding rsyslogd and journald logging'
tags : [RHCSA, RHCSA_mod, Configuring Logging, Understanding rsyslogd and journald logging]
categories: [RHCSA]
---


On RHEL 7 there are two systems responsible for logging : **rsyslogd**
and **journald**. These two services together to handle logging system
information.

It is up to the services (containing the logging information) to decide
how and where the log files will be written to. It is possible to write
directly to a log file anywhere (e.g., `/somewhere/my.log`). The service
may also choose to pass over the information to **systemctl** as well.
The `systemctl` utility is used to start the service and keep track of
the actions of the service while it’s starting. Anything that goes
through **systemd** will be writing to `journald`, which is the systemd
way of logging.

![Logging
Options<span label="fig:2 Logging Options"></span>](RHCSA/Mod2/chapters/2.14.a)

The classic way of logging is through the `rsyslogd` daemon, which
typically writes information to the `/var/log/` directory. Alternative
locations can also be used.

Given the various ways to log information, it may also become
challenging for a user to get that logged information. It can be through
**journalctl** or rsyslog. It is possible to tie these two systems
together.

## Sharing logging information

To ensure that journalctl information is automatically logged to
rsyslog, we need to add a couple of lines to `/etc/rsyslog.conf` and
`/etc/rsyslog.d/listend.conf`:

<span>M<span>0.47</span>|M<span>0.47</span></span> In
**/etc/rsyslog.conf** : \&In **/etc/rsyslog.d/listend.conf** :  

``` bash
$ModLoad imuxsock
$OmitLocalLogging off
```

&

``` bash
$SystemLogSocketName /run/systemd/journal/syslog
```

  

The above lines enable rsyslog to receive information logged by
journald. To enable the logging of rsyslog information in journald, we
only need to add in the `/etc/rsyslog.conf`:

``` bash
$Modload omjournal *.* :omjournal:
```

The above lines specify that any information being logged should be sent
to `omjournal` which is a part of journald. The information from there
is available from `journalctl`.

![Sharing logging information between rsyslog and
journald<span label="fig:2 Sharing logging information"></span>](RHCSA/Mod2/chapters/2.14.b)
