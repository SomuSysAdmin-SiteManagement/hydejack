---
layout : post
title : 'Using the yum command'
tags : [RHCSA, RHCSA_mod, Managing Software, Using the yum command]
categories: [RHCSA]
---


The `yum` command is a package manager and a meta package handler. The
following are some of the yum commands:

## yum search

`yum search` searches the given repositories for a suitable package.

``` console
# yum search nmap
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
* base: centos.excellmedia.net
* extras: centos.excellmedia.net
* updates: centos.excellmedia.net
======================== N/S matched: nmap ========================
nmap-frontend.noarch : The GTK+ front end for nmap
nmap-ncat.x86_64 : Nmap's Netcat replacement
nmap.x86_64 : Network exploration tool and security scanner

Name and summary matches only, use "search all" for everything.
```

## yum install

`yum install` installs the package passed as argument to it, after
installing all the required dependencies. When the `-y` option is used,
Yum doesn’t wait for a (Y/N) reply after showing the dependency list,
and proceeds to download and install the package.

``` console
# yum install -y nmap
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
* base: centos.excellmedia.net
* extras: centos.excellmedia.net
* updates: centos.excellmedia.net
Resolving Dependencies
--> Running transaction check
---> Package nmap.x86_64 2:6.40-7.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
Package        Arch             Version                   Repository      Size
================================================================================
Installing:
nmap           x86_64           2:6.40-7.el7              base           4.0 M

Transaction Summary
================================================================================
Install  1 Package

Total download size: 4.0 M
Installed size: 16 M
Downloading packages:
No Presto metadata available for base
nmap-6.40-7.el7.x86_64.rpm                                 | 4.0 MB   06:38
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

Some programs may have a script that needs to be run to setup and
configure it. In such cases, yum does it for us.

## yum list

The `yum list` command is used to list the packages installed on a
system, filtered on a specific criteria.

<span>lM<span>0.72</span></span> **Options** &**Description**  
**yum list all** \&Lists all available and installed packages  
**yum list installed** \&Only list the installed packages  
**yum list avialable** \&Only list the available packages  

## yum provides

Sometimes we don’t know which package to install. For example, if we
want to install and use *semanage*, an important utility to set up
SELinux, we have to use the `yum search semanage` command to find all
the info about the packages that offer it.

``` console
# yum search semanage
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
* base: centos.excellmedia.net
* extras: centos.excellmedia.net
* updates: centos.excellmedia.net
============================ N/S matched: semanage =============================
libsemanage-python.x86_64 : semanage python bindings for libsemanage
libsemanage.i686 : SELinux binary policy manipulation library
libsemanage.x86_64 : SELinux binary policy manipulation library
libsemanage-devel.i686 : Header files and libraries used to build policy
: manipulation tools
libsemanage-devel.x86_64 : Header files and libraries used to build policy
: manipulation tools
libsemanage-static.i686 : Static library used to build policy manipulation tools
libsemanage-static.x86_64 : Static library used to build policy manipulation
: tools

Name and summary matches only, use "search all" for everything.
```

The above are the results that contain the string ’*semanage*’ in their
names/descriptions, but may not contain the semanage binary that we
require. For such cases, where we know the name of the binary utility,
but don’t know which package contains it, we use the `yum provides`
command. The `*/semanage` is used to indicate it needs to search some
file pattern.

``` console
# yum provides */semanage
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
* base: centos.excellmedia.net
* extras: centos.excellmedia.net
* updates: centos.excellmedia.net
libsemanage-devel-2.5-8.el7.i686 : Header files and libraries used to build
: policy manipulation tools
Repo        : base
Matched from:
Filename    : /usr/include/semanage

libsemanage-devel-2.5-8.el7.x86_64 : Header files and libraries used to build
: policy manipulation tools
Repo        : base
Matched from:
Filename    : /usr/include/semanage

policycoreutils-python-2.5-17.1.el7.x86_64 : SELinux policy core python
: utilities
Repo        : base
Matched from:
Filename    : /usr/sbin/semanage
Filename    : /usr/share/bash-completion/completions/semanage

policycoreutils-python-2.5-17.1.el7.x86_64 : SELinux policy core python
: utilities
Repo        : @anaconda
Matched from:
Filename    : /usr/sbin/semanage
Filename    : /usr/share/bash-completion/completions/semanage
```

## yum remove

`yum remove <packageName>` checks the system to see if any installed
packages are dependent upon the package we’re trying to remove. If so,
it removes the specified package and the dependent packages, unless one
(or more) of them are protected. For example, `yum remove bash` fails as
it’d have to remove Systemd and yum packages since they are heavily
dependent on bash\! Again, any `yum remove` command requires a prompt to
be answered, which can be bypassed with `yum remove -y`.

``` console
# yum remove -y nmap
Loaded plugins: fastestmirror, langpacks
Resolving Dependencies
--> Running transaction check
---> Package nmap.x86_64 2:6.40-7.el7 will be erased
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
Package        Arch             Version                  Repository       Size
================================================================================
Removing:
nmap           x86_64           2:6.40-7.el7             @base            16 M

Transaction Summary
================================================================================
Remove  1 Package

Installed size: 16 M
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Erasing    : 2:nmap-6.40-7.el7.x86_64                                     1/1
Verifying  : 2:nmap-6.40-7.el7.x86_64                                     1/1

Removed:
nmap.x86_64 2:6.40-7.el7

Complete!
```

