---
layout : post
title : 'Understanding Jobs and Processes'
tags : [RHCSA, RHCSA_mod, Managing Processes, Understanding Jobs and Processes]
categories: [RHCSA]
---


During boot several services start up that in turn launch several
processes that run in the background. They can be viewed by the `ps aux`
command. Everything that’s happening on a Linux system has a **process**
behind it, which controls the actions. However, sometimes users launch a
process from the shell - then it’s called a **job**, related to that
specific shell.

Let us consider a situation where a job keeps a shell busy. Consider the
command is :

``` console
# dd if=/dev/zero of=/dev/null # Copies Nothing to Nowhere!
```

To stop the job from keeping the shell busy, we can move it to the
background. The first step is to stop the job using *CTRL+Z*. Then,
simply typing bg moves the job to the background\!

## jobs

The jobs command shows us an overview of all the jobs that are currently
running.

``` console
$ dd if=/dev/zero of=/dev/null
^Z
[1]+  Stopped                 dd if=/dev/zero of=/dev/null
$ jobs
[1]+  Stopped                 dd if=/dev/zero of=/dev/null
$ bg
[1]+ dd if=/dev/zero of=/dev/null &
$ jobs
[1]+  Running                 dd if=/dev/zero of=/dev/null &
```

Jobs are tied to the current shell and user account. Any user will only
see the jobs that were launched using the present shell when the `jobs`
command is used.
