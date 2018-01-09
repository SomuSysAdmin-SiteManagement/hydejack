---
layout : post
title : 'Understanding Kickstart Usage'
tags : [RHCSA, RHCSA_mod, Using Kickstart, Understanding Kickstart Usage]
categories: [RHCSA]
---


The purpose of using kickstart is to automatically provide the settings
we’d normally provide manually to the OS during installation. Now, to
use kickstart, we need to provide the kickstart file (which contains the
settings) at a place available to the installer.

Let us consider a minimal installation using *boot.iso*. Also, let us
assume the kickstart file is named `myks.cfg`. Either USB keys (or any
other storage media) or a server could host the file and provide it to
the installer. It doesn’t matter whether the installation is from a
local media (e.g., usb key/ DVD) or a repository server. Once the
installation starts, there is nothing more to do for the `SysAdmin` to
do but to wait for it to finish, and the kickstart installation needs no
manual intervention whatsoever\!
