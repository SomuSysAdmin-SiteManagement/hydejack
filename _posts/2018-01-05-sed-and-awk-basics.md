---
layout : post
title : 'sed and awk basics'
tags : [RHCSA, RHCSA_mod, Working with Text Files, grep]
categories: [RHCSA]
---


## sed

`sed` is an old utility that’s used to process text. Many of it’s
functionalities can now be done using `grep` itself.

### sed q

To see the first two lines of a file using `sed` we use:

``` console
$ sed 2q /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
```

### sed -n

The `-n` flag makes `sed` print no output unless the `p` flag is also
provided. Here, we use a Regular Expression *"root"* to match only a
certain part of the text and then the `p` flag to print only if that
criteria is matched.

``` console
$ sed -n /^root/p /etc/passwd
root:x:0:0:root:/root:/bin/bash
```

The above result could also be obtained with `grep "^root" /etc/passwd`.

### Substitution with sed

Sed can be used to substitute text within a file using the `s`
parameter. It’s used as :

``` console
$ cat names
Somu
Arpi
Neha
Santy
Debu
$ sed -i 's/Santy/Dickwad/g' names
$ cat names
Somu
Arpi
Neha
Dickwad
Debu
```

The `-i` flag asks the modifications to be made in place. Otherwise the
output (changed text) would’ve simply been displayed to the screen and
then need to be redirected to a file for storage.

## awk

awk is another utility that is especially useful when working with text
files. It excels at operations like cutting out information.

### Cutting out information using awk

For certain operations, the `awk` command is a lot more powerful than
the `cut` utility. In the example below `cut` has a hard time
recognizing the second field, while `awk` has no problem whatsoever\!

``` console
$ ps aux | grep 'gdm'
root       1117  0.0  0.1 480248  4688 ?        Ssl  09:20   0:00 /usr/sbin/gdm
root       1333  0.8  1.2 328524 47220 tty1     Ssl+ 09:20   0:43 /usr/bin/X :0 -background none -noreset -audit 4 -verbose -auth /run/gdm/auth-for-gdm-bgrBZH/database -seat seat0 -nolisten tcp vt1
root       1718  0.0  0.1 528944  5848 ?        Sl   09:20   0:00 gdm-session-worker [pam/gdm-password]
gdm        1737  0.0  0.1 458088  4152 ?        Sl   09:20   0:00 ibus-daemon --xim --panel disable
gdm        1741  0.0  0.1 373560  5424 ?        Sl   09:20   0:00 /usr/libexec/ibus-dconf
gdm        1745  0.0  0.2 438152  7772 ?        Sl   09:20   0:00 /usr/libexec/ibus-x11 --kill-daemon
somu      12218  0.0  0.0 112664   972 pts/0    R+   10:47   0:00 grep --color=auto gdm
$ ps aux | grep 'gdm' | cut -f 2
root       1117  0.0  0.1 480248  4688 ?        Ssl  09:20   0:00 /usr/sbin/gdm
root       1333  0.8  1.2 328524 47220 tty1     Ssl+ 09:20   0:43 /usr/bin/X :0 -background none -noreset -audit 4 -verbose -auth /run/gdm/auth-for-gdm-bgrBZH/database -seat seat0 -nolisten tcp vt1
root       1718  0.0  0.1 528944  5848 ?        Sl   09:20   0:00 gdm-session-worker [pam/gdm-password]
gdm        1737  0.0  0.1 458088  4152 ?        Sl   09:20   0:00 ibus-daemon --xim --panel disable
gdm        1741  0.0  0.1 373560  5424 ?        Sl   09:20   0:00 /usr/libexec/ibus-dconf
gdm        1745  0.0  0.2 438152  7772 ?        Sl   09:20   0:00 /usr/libexec/ibus-x11 --kill-daemon
somu      12226  0.0  0.0 112664   968 pts/0    R+   10:48   0:00 grep --color=auto gdm
$ ps aux | grep 'gdm' | awk '{ print $2 }'
1117
1333
1718
1737
1741
1745
12248
```
