---
layout : post
title : 'Understanding Special Permissions'
tags : [RHCSA, RHCSA_mod, Managing Permissions, Understanding Special Permissions]
categories: [RHCSA]
---


<span>P<span>0.1</span>ccM<span>0.22</span>M<span>0.37</span></span>
*Permission* &*Symbol* &*Value* &**Files** &**Directories**  
**Set User ID** &`u+s` &4 \&Run executable file as Owner &—  
**Set Group ID** &`g+s` &2 \&Run executable file with permissions of
Group-Owner \&Inherit group ownership to all newly created items in the
folder.  
**Sticky Bit** &`+t` &1 &— \&Allows to delete files in the directory
only if user is the owner or parent-directory-owner (or root).  

**SetUID** : This is a special case where we grant the file special
permission to be executed by any group or others (that have execution
permission on the file) as if the owner of the file were running it. So,
*the file executes with the same permission set as that of the owner*.

**SetGID** : This is a special case where we grant the file special
permission to be executed by any user or others (that have execution
permission on the file) as if the group-member of the file were running
it. So, *the file executes with the same permission set as that of the
group*.

Both SetUID and SetGID are dangerous permissions when applied to file
and should be avoided if possible\!

**Sticky Bit** : While it has no effect when applied on a file, when
applied to a directory, especially in case of shared directories, one
user cannot delete the file of another user (owner of the file), unless
the user is owner of the directory or root.
