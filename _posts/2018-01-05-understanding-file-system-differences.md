---
layout : post
title : 'Understanding File System Differences'
tags : [RHCSA, RHCSA_mod, Managing Partitions, Understanding File System Differences]
categories: [RHCSA]
---


For a RHEL 7 installation, there are several file system choices:

<span>lM<span>0.84</span></span> **File System** &**Description**  
**XFS** \&The default file system for RHEL 7 and many others, built with
scalability in mind. Based on a B-Tree database, which specializes in
disk space allocation with high speed and makes looking up files really
easy. It also has different tuning options for varying workloads.  
**Ext4** \&This was the default filesystem till RHEL 6. It was based on
1993’s Ext2 file system which was built to handle much smaller disks
than our current needs. It uses H-Tree indexing, which is use of basic
index files - suitable for thousands of files, but not practical or
economical (in terms of time) for millions of files, which our systems
demand. While it is not as scalable as XFS, it does provide backwards
compatibility. Thus, for best performance, it shouldn’t be used as a
default file system.  
**Btrfs** \&This is a Copy-on-Write(CoW) file system, which means that
before writing to a file, that file is copied somewhere else, thus
making journaling unnecessary\! Journalling is the system where the
filesystem keeps track of the changes being made to the file to make
rolling back possible. This also makes undelete operations unnecessary
(which have never worked on Linux anyway). It even has added features
like subvolumes. It wasn’t however included in RHEL 7 First Customer
Shipment (FCS).  
**vfat** \&Primarily for compatibility with other OSs, such as Windows.
It is particularly useful for removable media such as USB sticks. This
filesystem is not needed to be installed on the hard disk of the server
however, even in cases where Samba provides access to files on the
server (Samba handles the file system differences and translation
itself).  
**GFS2** \&For Active/Active High Availability (HA) Clustering
Environments. Only needed when multiple nodes need to write to the same
file system concurrently. For Active/Passive File HA Clusters,
XFS/Ext4/etc. suffice.  
**Gluster** \&Gluster is a distributed file system. Thus, even though
represented under the same hierarchy, it can reside on multiple servers.
Storage is configured to be done in *bricks* that are spread over
servers. Each brick uses XFS as their back-end file system. This is an
important file system for cloud environments.
