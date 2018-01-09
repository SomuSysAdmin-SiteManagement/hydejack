---
layout : post
title : 'Understanding logrotate'
tags : [RHCSA, RHCSA_mod, Configuring Logging, Understanding logrotate]
categories: [RHCSA]
---


Logrotate is a system that’s used to ensure that logging doesn’t fill
the hard disk of the server. Logrotate ensures that after a specified
amount of time, log files will be closed and new ones opened, and a
backlog of a couple of log files will be kept. This is all done based on
the assumption that old logs are useless beyond a certain age. This has
a stark disadvantage of erasing logging data that may become useful at a
later date.

A solution to this problem is the use of a log server, with sufficient
hard disk space to keep about a year’s worth of logs. The client
machines can have logrotate setup to keep only a couple of week’s data
since there will be a backup available on the log server.
