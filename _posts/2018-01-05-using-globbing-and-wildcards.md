---
layout : post
title : 'Using Globbing and Wildcards'
tags : [RHCSA, RHCSA_mod, Using Essential Tools, Using Globbing and Wildcards]
categories: [RHCSA]
---


|                 |    |                                                                                                          |
| :-------------- | :-: | :------------------------------------------------------------------------------------------------------- |
| **$ ls a\***    | \- | Lists all files and folders (including contents of each folder) that start with "a"                      |
| **$ ls \*a\***  | \- | Lists all files and folders that contain the string "a".                                                 |
| **$ ls -d a\*** | \- | Shows all files and folders that start with "a" but excludes the contents of each individual folder.     |
| **$ ls ??st\*** | \- | Lists all files and folders that have "st" as the \(3^{rd}\) and the \(4^{th}\) character in their name. |
| **$ \[a-f\]**   | \- | Indicates any character provided within the range of a to f.                                             |
