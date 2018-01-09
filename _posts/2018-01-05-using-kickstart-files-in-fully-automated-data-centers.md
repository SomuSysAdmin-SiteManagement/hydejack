---
layout : post
title : 'Using Kickstart files in fully automated data-centers'
tags : [RHCSA, RHCSA_mod, Using Kickstart, Using the Kickstart file for Automatic installations]
categories: [RHCSA]
---


There are two servers here - one on the left on which the OS will be
installed, and the other on the right, the installation server, which
contains the boot image and installer. The server on which the OS will
be installed performs a PXE boot (read as *pixie* boot), which is
essentially booting from an installation image on the network through
the NIC, while a server (installation server) is waiting for it.

On the installation server there is a **DHCP** (Dynamic Host
Configuration Protocol) server and a **TFTP** (Trivial File Transfer
Protocol). The DHCP server hands out IP addresses and the TFTP server
makes the boot image available for download and installation. This boot
image is received on the server on which the OS will be installed, and
the installer is loaded. On a normal network installation this is where
the network’s role would end.

![PXE (Network) boot with
Kickstart<span label="fig:3 Network boot with Kickstart"></span>](RHCSA/Mod3/chapters/3.18.a)

However, since here the OS is installed using a Kickstart file, the
client sends a request to receive a copy of the kickstart file from the
web/FTP server on the installation server. Upon receiving the kickstart
file, finally the installation begins. Finally, the installation needs
access to the repository on the installation server for the rpm files
that the installer will install (since the installation server is also
the installation source). The repository needs to be pre-configured on
the installation server as well.
