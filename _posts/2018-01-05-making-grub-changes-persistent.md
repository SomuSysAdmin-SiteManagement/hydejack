---
layout : post
title : 'Making Grub Changes persistent'
tags : [RHCSA, RHCSA_mod, Applying Essential Troubleshooting Skills, Making Grub Changes persistent]
categories: [RHCSA]
---


## Changes made during boot

After making changes in the boot menu, when we finally boot, we can make
those changes persistent by rewriting the `/boot/grub2/grub.cfg` file.

``` console
# grub2-mkconfig -o /boot/grub2/grub.cfg
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.10.0-693.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-693.el7.x86_64.img
Found linux image: /boot/vmlinuz-0-rescue-5cbfb880c0aa466ca7e3be91308fde5f
Found initrd image: /boot/initramfs-0-rescue-5cbfb880c0aa466ca7e3be91308fde5f.img
done
```

## Changes made in Configuraiton File

The `/etc/default/grub` file is the configuration file for Grub2 that
provides several boot options. These can be changed to affect several
boot parameters, and the changes saved to the bootloader. There are also
shell scripts in the `/etc/grub.d` directory that aren’t meant to be
touched by an administrator. These control grub boot procedure as well.
Almost all the functionality that we need from grub is provided by a set
of grub2 commands:

``` console
# grub2-
grub2-bios-setup           grub2-mkpasswd-pbkdf2
grub2-editenv              grub2-mkrelpath
grub2-file                 grub2-mkrescue
grub2-fstest               grub2-mkstandalone
grub2-get-kernel-settings  grub2-ofpathname
grub2-glue-efi             grub2-probe
grub2-install              grub2-reboot
grub2-kbdcomp              grub2-rpm-sort
grub2-menulst2cfg          grub2-script-check
grub2-mkconfig             grub2-set-default
grub2-mkfont               grub2-setpassword
grub2-mkimage              grub2-sparc64-setup
grub2-mklayout             grub2-syslinux2cfg
grub2-mknetdir
```

These commands can be used to accomplish tasks with grub such as install
grub (`grub2-install`), make a new boot image (`grub2-mkimage`), set a
grub boot password (`grub2-mkpasswd-pbkdf2`), to probe operating system
configuration (`grub2-probe`), to reboot a specific boot image
(`grub2-reboot`) and much more.
