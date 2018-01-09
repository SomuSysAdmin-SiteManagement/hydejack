---
layout : post
title : 'Understanding Cron Configuration files and Execution times'
tags : [RHCSA, RHCSA_mod, Scheduling Tasks, Understanding Cron Configuration files and Execution times]
categories: [RHCSA]
---


The main configuration file for cron is located at `/etc/crontab`. The
default contents of the file is:

``` bash
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
```

This config file lists the meaning of the time specifications for
setting up a cronjob. We shouldn’t modify this file as it’ll be
over-written every time the software is updated.

## crontab -e

One of the recommended ways to create a new cronjob is to use the
command `crontab -e`. Each user has his own *crontab* that contains
instructions to the cron daemon to execute certain tasks at a certain
time. The `crontab -e` command opens the user’s crontab in editor mode
and creates a cron config file for the present user.

## Other cron config files

Many other cron config files are located in the `/etc` directory.

``` console
$ ls -ld cron*
drwxr-xr-x. 2 root root  54 Nov 25 08:59 cron.d
drwxr-xr-x. 2 root root  57 Nov 25 08:59 cron.daily
-rw-------. 1 root root   0 Aug  3 21:03 cron.deny
drwxr-xr-x. 2 root root  41 Nov 25 08:59 cron.hourly
drwxr-xr-x. 2 root root   6 Jun 10  2014 cron.monthly
-rw-r--r--. 1 root root 451 Jun 10  2014 crontab
drwxr-xr-x. 2 root root   6 Jun 10  2014 cron.weekly
```

Each of the folders named *cron.hourly*, *cron.daily*, *cron.weekly* and
*cron.monthly* are used by RPMs to drop shell scripts that the cron
daemon automatically executes on an appropriate schedule.

For example, the `/etc/cron.daily` has a file called *man-db.cron*. The
`mandb` command (executed appropriately by this file) has to be executed
periodically to rebuild the index that the man pages that we search
using `man -k`. The file contains:

``` bash
$ cat man-db.cron
#!/bin/bash

if [ -e /etc/sysconfig/man-db ]; then
. /etc/sysconfig/man-db
fi

if [ "$CRON" = "no" ]; then
exit 0
fi

renice +19 -p $$ >/dev/null 2>&1
ionice -c3 -p $$ >/dev/null 2>&1

LOCKFILE=/var/lock/man-db.lock

# the lockfile is not meant to be perfect, it's just in case the
# two man-db cron scripts get run close to each other to keep
# them from stepping on each other's toes.  The worst that will
# happen is that they will temporarily corrupt the database
[[ -f $LOCKFILE ]] && exit 0

trap "{ rm -f $LOCKFILE ; exit 0; }" EXIT
touch $LOCKFILE
# create/update the mandb database
mandb $OPTS

exit 0
```

The file is *not* a typical cronjob since we don’t need to tell the cron
daemon when to execute it. It knows that the given shell script has to
be executed once daily. There is no fixed time for the cron job to run,
and thus, even if the system goes down during a certain period of time,
the cron daemon will execute the job at a later time.

## cron.d

The files in this directory look a lot more like the crontab files. Some
of the contents of this directory are:

``` console
$ ls -l /etc/cron.d
total 12
-rw-r--r--. 1 root root 128 Aug  3 21:03 0hourly
-rw-r--r--. 1 root root 108 Jun 13 19:38 raid-check
-rw-------. 1 root root 235 Aug  3 15:00 sysstat
```

The contents of the *sysstat* file is:

``` bash
$ sudo cat sysstat
# Run system activity accounting tool every 10 minutes
*/10 * * * * root /usr/lib64/sa/sa1 1 1
# 0 * * * * root /usr/lib64/sa/sa1 600 6 &
# Generate a daily summary of process accounting at 23:53
53 23 * * * root /usr/lib64/sa/sa2 -A
```

The format followed is : time specification, name of the user under
which the command has to be executed, followed by the command to be
executed.

Thus, if a cronjob has to be run as an administrator, we should put it
in a cron file in the `/etc/cron.d` directory. If however, a
user-specific cron file has to be executed, then it’s better to use the
`crontab -e` command to generate and store the cronjobs for that user.
