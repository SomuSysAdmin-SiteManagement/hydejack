---
layout : post
title : 'Integrating rsyslogd and journald'
tags : [RHCSA, RHCSA_mod, Configuring Logging, Integrating rsyslogd and journald]
categories: [RHCSA]
---


Rsyslog is the old process of logging system information, and journald
is the new process that’s trying to do the same. Both of them are built
to log process information.

## rsyslog

Let us consider a process that’s trying to write what it’s doing in log
files. It has two options: rsyslog and journald. If it writes to
rsyslog, the advantage is that rsyslog is the system process that can
handle logging for all processes. Some processes, however, just have
some internal logging option (direct writes) thus bypassing rsyslog. A
well known example of such a process is the *apache web server*. This is
a disadvantage as if every process is doing its own logging, it’s harder
to manage the whole thing in a centralized way. These processes can
however, be configured to write to rsyslog anyway\!

## journald

Journald is controlled by **systemd**, which takes care of starting
services while booting. Systemd writes its own information to journald,
and thus, if something goes wrong while starting a service, the
information is available from *journald*.

For RHEL 7 in general, *journald* generally takes care of loggin the
startup information logging while *rsyslog* takes care of logging
current activity of processes.
