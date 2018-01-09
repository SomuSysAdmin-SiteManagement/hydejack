---
layout : post
title : 'Using rpm queries'
tags : [RHCSA, RHCSA_mod, Managing Software, Using the yum command]
categories: [RHCSA]
---


Any software installed on our RHEL Servers are tracked in an rpm
database, which supports queries to find out status and other
information about packages. Rpm queries are most useful for SysAdmins
when we need to find out more information about a package or software.
For example, if we need to find out how to configure a time
synchronization service called `chronyd`, first we find out where it is
located.

``` console
# which chronyd
/usr/sbin/chronyd
```

Now that we know where the binary for the chrony daemon is located, we
perform an rpm query on it, to find out which package it comes from:

``` console
# rpm -qf /usr/sbin/chronyd     # Query the package owning <file>
chrony-3.1-2.el7.centos.x86_64
```

Now that we know what package it comes from, we can list everything that
the package `chrony` contains:

``` console
# rpm -ql chrony    # Query list
/etc/NetworkManager/dispatcher.d/20-chrony
/etc/chrony.conf
/etc/chrony.keys
/etc/dhcp/dhclient.d/chrony.sh
/etc/logrotate.d/chrony
/etc/sysconfig/chronyd
/usr/bin/chronyc
/usr/lib/systemd/ntp-units.d/50-chronyd.list
/usr/lib/systemd/system/chrony-dnssrv@.service
/usr/lib/systemd/system/chrony-dnssrv@.timer
/usr/lib/systemd/system/chrony-wait.service
/usr/lib/systemd/system/chronyd.service
/usr/libexec/chrony-helper
/usr/sbin/chronyd
/usr/share/doc/chrony-3.1
/usr/share/doc/chrony-3.1/COPYING
/usr/share/doc/chrony-3.1/FAQ
/usr/share/doc/chrony-3.1/NEWS
/usr/share/doc/chrony-3.1/README
/usr/share/man/man1/chronyc.1.gz
/usr/share/man/man5/chrony.conf.5.gz
/usr/share/man/man8/chronyd.8.gz
/var/lib/chrony
/var/lib/chrony/drift
/var/lib/chrony/rtc
/var/log/chrony
```

To see only the configuration files, instead of all files related to the
package, we use:

``` console
# rpm -qc chrony    # Query config
/etc/chrony.conf
/etc/chrony.keys
/etc/logrotate.d/chrony
/etc/sysconfig/chronyd
```

To find the documentation for the package, we use:

``` console
# rpm -qd chrony    # Query documentation
/usr/share/doc/chrony-3.1/COPYING
/usr/share/doc/chrony-3.1/FAQ
/usr/share/doc/chrony-3.1/NEWS
/usr/share/doc/chrony-3.1/README
/usr/share/man/man1/chronyc.1.gz
/usr/share/man/man5/chrony.conf.5.gz
/usr/share/man/man8/chronyd.8.gz
```

To view all packages installed on our system, we can use:

``` console
# rpm -qa   # Query all
```

This command is especially useful to find out which version of a package
is installed.

``` console
# rpm -qa | grep openjdk
java-1.8.0-openjdk-headless-1.8.0.151-1.b12.el7_4.x86_64
java-1.8.0-openjdk-1.8.0.151-1.b12.el7_4.x86_64
```

### Pre and Post install Scripts

Many packages include pre and post installation scripts that we may need
to find out about. If that is the case, we can use:

``` console
# rpm -q --scripts java-1.8.0-openjdk
postinstall scriptlet (using /bin/sh):

update-desktop-database /usr/share/applications &> /dev/null || :
/bin/touch --no-create /usr/share/icons/hicolor &>/dev/null || :
exit 0
postuninstall scriptlet (using /bin/sh):

update-desktop-database /usr/share/applications &> /dev/null || :
if [ $1 -eq 0 ] ; then
/bin/touch --no-create /usr/share/icons/hicolor &>/dev/null
/usr/bin/gtk-update-icon-cache /usr/share/icons/hicolor &>/dev/null || :
fi
exit 0
posttrans scriptlet (using /bin/sh):

/usr/bin/gtk-update-icon-cache /usr/share/icons/hicolor &>/dev/null || :
```

This step become critical when working on a production server,
especially for security purposes since installing a package requires
administrative (root) privileges. If the package is from an unverified
source, we should know what exactly the package installation script does
before executing it.

For 3\(^{rd}\) party, downloaded packages, that we might not have
installed yet, we need to use the `rpm -qp` command instead. Thus, to
list the contents of said 3rd party package, we use:

``` console
# rpm -qpl <packageName>.rpm
# rpm -qp --scripts <packageName>.rpm
```

The second line shows us the scripts (pre and post install) that’ll be
used by the downloaded (and NOT yet installed) package.

## Installing a local rpm file

To perform the installation of an rpm file that we’ve downloaded from
the internet, and it’s not in a repository, we use `yum localinstall`.

To download said rpm, we can use a tool like `wget <rpmURL>`.

``` console
# ls -l
total 4056
-rw-r--r--. 1 root root 4152356 Nov 25  2015 nmap-6.40-7.el7.x86_64.rpm
# yum localinstall nmap-6.40-7.el7.x86_64.rpm
Loaded plugins: fastestmirror, langpacks
Examining nmap-6.40-7.el7.x86_64.rpm: 2:nmap-6.40-7.el7.x86_64
Marking nmap-6.40-7.el7.x86_64.rpm to be installed
Resolving Dependencies
--> Running transaction check
---> Package nmap.x86_64 2:6.40-7.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
Package    Arch         Version            Repository                     Size
================================================================================
Installing:
nmap       x86_64       2:6.40-7.el7       /nmap-6.40-7.el7.x86_64        16 M

Transaction Summary
================================================================================
Install  1 Package

Total size: 16 M
Installed size: 16 M
Is this ok [y/d/N]: y
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : 2:nmap-6.40-7.el7.x86_64                                     1/1
Verifying  : 2:nmap-6.40-7.el7.x86_64                                     1/1

Installed:
nmap.x86_64 2:6.40-7.el7

Complete!
```

## repoquery

The repoquery is similar to the rpm query, but instead of querying an
installed or not-yet-installed but locally available package, it
directly queries the repositories, without even needing to download
them\! However, the `--scripts` option isn’t yet supported by the
command.

``` console
# repoquery -ql yp-tools
/usr/bin/ypcat
/usr/bin/ypchfn
/usr/bin/ypchsh
/usr/bin/ypmatch
/usr/bin/yppasswd
/usr/bin/ypwhich
/usr/sbin/yppoll
/usr/sbin/ypset
/usr/sbin/yptest
/usr/share/doc/yp-tools-2.14
/usr/share/doc/yp-tools-2.14/AUTHORS
/usr/share/doc/yp-tools-2.14/COPYING
/usr/share/doc/yp-tools-2.14/ChangeLog
/usr/share/doc/yp-tools-2.14/NEWS
/usr/share/doc/yp-tools-2.14/README
/usr/share/doc/yp-tools-2.14/THANKS
/usr/share/doc/yp-tools-2.14/TODO
/usr/share/doc/yp-tools-2.14/nsswitch.conf
/usr/share/locale/de/LC_MESSAGES/yp-tools.mo
/usr/share/locale/sv/LC_MESSAGES/yp-tools.mo
/usr/share/man/man1/ypcat.1.gz
/usr/share/man/man1/ypchfn.1.gz
/usr/share/man/man1/ypchsh.1.gz
/usr/share/man/man1/ypmatch.1.gz
/usr/share/man/man1/yppasswd.1.gz
/usr/share/man/man1/ypwhich.1.gz
/usr/share/man/man5/nicknames.5.gz
/usr/share/man/man8/yppoll.8.gz
/usr/share/man/man8/ypset.8.gz
/usr/share/man/man8/yptest.8.gz
/var/yp/nicknames
```

## Displaying information about a package

`repoquery -qi <packageName>` can display information about the package.

``` console
# repoquery -qi awesum

Name        : awesum
Version     : 0.6.0
Release     : 1
Architecture: noarch
Size        : 150637
Packager    : Darren L. LaChausse <the_trapper@users.sourceforge.net>
Group       : Applications/Security
URL         : http://awesum.sf.net/
Repository  : Ex11Repo
Summary     : Awesum is an easy to use graphical checksum verifier.
Source      : awesum-0.6.0-1.src.rpm
Description :
Awesum is a graphical checksum verification utility. It is written in Python
and uses the PyGTK toolkit. Awesum is very easy to use and includes support
for both MD5 and SHA checksum algorithms. Unlike many checksum verification
utilities, Awesum features a progress bar which makes working with large files
(such as CD-ROM ISO images) much more bearable.
```

