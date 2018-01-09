---
layout : post
title : 'Boot Procedure Generic Overview'
tags : [RHCSA, RHCSA_mod, Managing and Understanding the Boot Procedure, Boot Procedure Generic Overview]
categories: [RHCSA]
---


On starting up, the computer perform a "Power On Self Test" (POST).
During this, the computer checks all the connected hardware and finds
the boot device, which is typically a hard disk / solid state drive
(HDD/SSD). On the boot device, the computer access Grub 2, the boot
loader, that loads the **kernel** and **initrd**.

![Boot
Procedure<span label="fig:3 Boot Procedure"></span>](RHCSA/Mod3/chapters/3.19.a)

Next, the root file system is mounted (by the kernel) and then,
**systemd** is started. Once systemd has started, everything else can
begin, such as: logging, mounting the other file systems, starting all
services and preparing the login shell.
