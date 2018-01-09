---
layout : post
title : 'Understanding Basic Permissions'
tags : [RHCSA, RHCSA_mod, Managing Permissions, Understanding Basic Permissions]
categories: [RHCSA]
---


| *Permission* | **Files**                      | **Directories**                                      |
| :----------: | :----------------------------- | :--------------------------------------------------- |
|    **r**     | Opening and outputting a file. | List files in a directory.                           |
|              |                                | The user **can’t** read all files in that directory. |
|              |                                | For that, he needs read access on the                |
|              |                                | individual files.                                    |
|    **w**     | Modify contents of the file    | Modify contents of the directory, i.e., add,         |
|              |                                | delete, move, etc. files in that directory.          |
|    **x**     | If the contents of the file is | User can `cd` into the directory.                    |
|              | executable, the user can       |                                                      |
|              | execute it.                    |                                                      |

The fact that no file on a linux system has an executable permission by
default is one of the core factors that makes th OS so secure. For
example, even if a user were to get an email attachment with malware, it
won’t be able to run without execute permissions\!
