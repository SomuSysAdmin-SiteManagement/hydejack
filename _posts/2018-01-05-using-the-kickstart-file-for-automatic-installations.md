---
layout : post
title : 'Using the Kickstart file for Automatic installations'
tags : [RHCSA, RHCSA_mod, Using Kickstart, Using the Kickstart file for Automatic installations]
categories: [RHCSA]
---


For network booting using a kickstart file, first we copy the kickstart
file to a FTP server.

``` console
# scp -P 22 automate-install-ks.cfg somu@infraServer.somuVMnet.com:~
# ssh -p 22 root@infraServer.somuVMnet.com
# cp automate-install-ks.cfg /var/ftp/pub
# cd /var/ftp/pub
# chmod 644 automate-install-ks.cfg # Others need read access to be able to use the kickstart file.
# systemctl status vsftpd # Checking if the FTP server is functioning correctly!
```

Now on the machine where the OS is being installed, when the installer
has fully loaded and provides the option to either directly install the
OS, or test installation media and then install the OS, we need to press
tab to set the installation options.

There, at the end, we need to type provide the location of the kickstart
file on the network:

``` bash
ks=ftp://infraServer.somuVMnet.com/ftp/pub/automated-install-ks.cfg
```

Then depending on which options were not given in the kickstart file,
the installer may ask for some options, but if all the necessary details
are given, then no manual intervention will be required at all\!

In cases of data-centers and places where a large number of servers need
to be installed, the use of a DVD is impractical and a installation
server should be set up.
