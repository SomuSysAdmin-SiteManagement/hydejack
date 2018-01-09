---
layout : post
title : 'Configuring logrotate'
tags : [RHCSA, RHCSA_mod, Configuring Logging, Understanding logrotate]
categories: [RHCSA]
---


The configuration files for `logrotate` reside in the `/etc` directory.
The generic logrotate configuration is stored in `/etc/logrotate.conf`
while the directory `/etc/logrotate.d` contains include files that RPMs
dump in it for package specific log rotation. Anything in the
`logrotate.d` directory will always overwrite the settings in
`logrotate.conf`. Typical contents of the `logrotate.conf` file is:

``` bash
# see "man logrotate" for details
# rotate log files weekly
weekly

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
dateext

# uncomment this if you want your log files compressed
#compress

# RPM packages drop log rotation information into this directory
include /etc/logrotate.d

# no packages own wtmp and btmp -- we'll rotate them here
/var/log/wtmp {
monthly
create 0664 root utmp
minsize 1M
rotate 1
}

/var/log/btmp {
missingok
monthly
create 0600 root utmp
rotate 1
}
```

The last two settings demonstrate how specific logrotation instructions
can be given for specific files. For example, the `/var/log/wtmp` file
has to be rotated monthly, and only 1 copy of the backlog is maintained.

Since logrotate doesn’t need to run all the time, it doesn’t itself run
as a service, but as a cron job\! The config file for logrotate cron job
is `/etc/cron.daily/logrotate`.

## Checking avialable hard disk space

The available hard disk space and the disk space occupied by a certain
directory can be checked using these two commands:

``` console
# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root  3.8G  3.4G  412M  90% /
devtmpfs                 2.9G     0  2.9G   0% /dev
tmpfs                    2.9G     0  2.9G   0% /dev/shm
tmpfs                    2.9G  9.2M  2.9G   1% /run
tmpfs                    2.9G     0  2.9G   0% /sys/fs/cgroup
/dev/mapper/centos-home  7.5G   65M  7.4G   1% /home
/dev/mapper/centos-var   1.9G  327M  1.6G  18% /var
/dev/sda2                485M  227M  258M  47% /boot
tmpfs                    580M  4.0K  580M   1% /run/user/42
tmpfs                    580M   28K  580M   1% /run/user/1000
/dev/sr0                 8.1G  8.1G     0 100% /run/media/somu/CentOS 7 x86_64
tmpfs                    580M     0  580M   0% /run/user/0
# du -hs /var/log
13M /var/log
```

