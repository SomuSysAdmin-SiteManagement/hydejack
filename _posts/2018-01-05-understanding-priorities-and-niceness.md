---
layout : post
title : 'Understanding Priorities and Niceness'
tags : [RHCSA, RHCSA_mod, Managing Processes, Understanding Priorities and Niceness]
categories: [RHCSA]
---


Consider a hypothetical scenario where a bunch of people are standing in
a queue to get movie tickets. There, everyone in the queue has the same
priority. Now, if a lady comes along and skips the queue, we can fairly
say she isn’t nice. The same goes for processes in a Linux system.

The processes are born with the same priority, but their niceness can be
adjusted. If the niceness is negative, the process is served first, and
if positive, it lets the other processes (with lower niceness) be served
before itself\!
