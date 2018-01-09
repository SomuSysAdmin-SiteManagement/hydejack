---
layout : post
title : 'Understanding Links'
tags : [RHCSA, RHCSA_mod, Essential File Management Tools, Understanding Links]
categories: [RHCSA]
---


**inode** - An inode is a datastructure that describes a file system
object such as a file or a directory, containing both the disc block
locations as well as the attributes of the file system object. The
inodes are identified by their inode number.

Consequently, for us to access the files/directories, we need to be able
to provide a name to the inodes, which are called hardlinks. A file may
have more than one hardlink. Note that each hardlink is simply a
different name provided tot he same inode. Ths, all hardlinks to the
same file/directory have the same inode number. Hardlinks are
one-directional only, i.e., the hardlink itself knows which inode it
points to, but the inodes only know the total number of hardlinks that
are associated with it, and not which exact ones are pointing to it.
Since hardlinks point to some inode, they always need to stay on the
same partition as the inode.

A symbolic link on the other hand, points to a hardlink instead of an
inode. As such, it has a different inode number than the one that the
hardlink points to. Thus, the hardlink and symbolic link can be on
different partitions as well. It can even exist across servers. Whenever
a hardlink is deleted, however, all the symbolic links pointing to it
are rendered invalid.
