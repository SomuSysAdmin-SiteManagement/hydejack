---
layout : post
title : 'Finding Files'
tags : [RHCSA, RHCSA_mod, Essential File Management Tools, Finding Files]
categories: [RHCSA]
---


The **find** command is used to find a file within a folder and its
subdirectories. When the starting point of the search is the root
directory (/) then find will search the entire file system. While the
utility is extremely thorough, this may cause delays due to remote
devices on the network mounted on the file system.

``` console
$ find / -name "passwd"
```

If you’re trying to find the location of a binary file, a better command
would be **which** command, as it directly shows the location of the
binary, but be careful as it only works with binaries.

``` console
$ which passwd
/usr/bin/passwd
```

Contrastingly, the command **whereis** not only gives us the locaiton of
the binary, but the location of the complete environment of the binary\!

``` console
$ whereis passwd
passwd: /usr/bin/passwd /etc/passwd /usr/share/man/man1/passwd.1.gz /usr/share/man/man5/passwd.5.gz
```

Another similar utility is called **locate** which shows all files that
have the string provided to it in its name. Note, however, that locate
operates on a database, that must be updated (especially after the
creation of a new file) to show relevant results.

``` console
# touch sinha
# ls
sinha
# locate sinha
/usr/share/vim/vim74/keymap/sinhala-phonetic_utf-8.vim
/usr/share/vim/vim74/keymap/sinhala.vim
# updatedb
# locate sinha
/home/somu/Documents/sinha
/usr/share/vim/vim74/keymap/sinhala-phonetic_utf-8.vim
/usr/share/vim/vim74/keymap/sinhala.vim
```
