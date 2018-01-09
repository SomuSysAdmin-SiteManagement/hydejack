---
layout : post
title : 'Scheduling with cron'
tags : [RHCSA, RHCSA_mod, Scheduling Tasks, Scheduling with cron]
categories: [RHCSA]
---


One of the best ways to run cronjobs is to become the user that we want
to run the cronjob as and then open their crontab, using the `crontab
-e` command.

Let us consider a hypothetical scenario where we want to run a specific
set of commands at 2.30PM everyday. Now, we first write the minutes(30)
followed by the hour in 24-hour format \[military time\](2PM=14). Next,
we want the script to run everyday, so we mark the day of the month, the
month and the day of the week with `*`s (everyday of the month, every
month and everyday of the week). Let us consider we want the cronjob to
write something to the syslog using the `logger` command. Then the entry
in the crontab will look like:

``` bash
30 14 * * * logger Hello
```

Typically, the `anacron` utility takes care of executing the shell
scripts in the *cron.hourly, cron.daily, cron.weekly and cron.monthly*
directories. It ensures that the commands will be executed at
appropriate times (that cannot be controlled by the user) if the machine
is down on the originally scheduled time.
