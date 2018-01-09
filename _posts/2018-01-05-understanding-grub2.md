---
layout : post
title : 'Understanding Grub2'
tags : [RHCSA, RHCSA_mod, Managing and Understanding the Boot Procedure, Understanding Grub2]
categories: [RHCSA]
---


The very first thing from the linux perspective (i.e., the first thing
that’s executed) when a computer boots is Grub2 (Grand Unified
Boot-loader).

The `/etc/default/grub` is the most important configuration file for
Grub2. Most of the customizations/modifications by an user is done to
this file. There are also additional configuration files in the
`/etc/grub.d` directory. If any of the configuration files have been
updated, the boot loader needs to be updated as well, by using the
`grub2-mkconfig` command. This updates the data in the Master Boot
Record (MBR) and the metadata in the first few sectors of our hard
drives.

Once the computer boots, we can access the Grub boot menu by pressing
the *escape* key. When this is done, we can enter special boot
instructions on it.

## Booting in emergency mode

On the boot menu, we need to enter `systemd.unit=emergency.target` as a
boot option to start up the computer in emergency mode, which is used in
case the computer can’t boot normally.

The diagram below explains the entire boot procedure. Once the power is
supplied to the computer, it performs the Power On Self Test and then
loads the boot loader from the MBR. Now, we have the option to enter the
boot menu by pressing the escape key, and enter the boot options, like
booting in emergency mode.

![Booting in emergency
mode<span label="fig:3 Booting in emergency mode"></span>](RHCSA/Mod3/chapters/3.19.b)

In case there’s something wrong with the bootloader itself, we can make
permanent modifications by editing the files: `/etc/default/grub` and
the config files in `/etc/grub.d` directory. Once these modifications
have been written, the boot loader needs to be updated using
`grub2-mkconfig` command. This ensures that the next time the MBR will
be read, the edited grub2 configuration files will be used.
