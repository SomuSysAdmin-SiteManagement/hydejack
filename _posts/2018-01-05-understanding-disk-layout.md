---
layout : post
title : 'Understanding Disk Layout'
tags : [RHCSA, RHCSA_mod, Managing Partitions, Understanding Disk Layout]
categories: [RHCSA]
---


There are two basic ways of organizing data on a hard disk : Partitions
and LVM (Logical Volume Management). Some parts of a hard disk need to
be configured with a fixed amount of storage. In such cases we use
partitions. This is applicable for `/boot` and `/` in the figure.
However, certain directories contain dynamic user data, and thus need to
be able to grow to any size. In such cases, the partitions don’t work
and we need to use Logical Volumes. In the image below, `sda1, sda2 &
sda3` are all Physical Volume(PV)s or partitions. In linux, each
partition needs to be connected to one or more directories in order to
be used.

![Disk
Layout<span label="fig:2 Disk Layout"></span>](RHCSA/Mod2/chapters/2.15.a)

In the case of Logical Volumes (LVs), just like partitions, there needs
to be a Physical Volume (PV). This PV is then put in a Volume Group
(Vg), represented by the red boundary lines in the image above. From
this volume group, we can create Logical Volumes (represented by the
blue lines). The advantage of this method is the unused space between
different LVs can be added to any of the LVs, and thus no disk space is
wasted and no directory in the LV is going to be full while another is
barely filled. In LVM it’s very easy to grow a logical volume later\!
