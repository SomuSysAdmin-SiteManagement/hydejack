---
layout : post
title : 'Working with tar'
tags : [RHCSA, RHCSA_mod, Essential File Management Tools, Working with Links]
categories: [RHCSA]
---


**tar** stands for Tape Archive. The command is most commonly used to
make backups of files by storing them in archives. Some of the options
of `tar`
are:

|        |    |               |    |                                                                       |
| :----- | :-: | :------------ | :-: | :-------------------------------------------------------------------- |
| **-c** | \- | create        | \- | typically has an extension of .tar                                    |
| **-t** | \- | show contents | \- | show contents of the archive.                                         |
| **-x** | \- | extract       |    |                                                                       |
| **-z** | \- | file          | \- | compress the archive using gzip. Typically has an extension of `.tgz` |
| **-v** | \- | verbose       | \- | tell us what the utility is doing.                                    |
| **-f** | \- | file          | \- | option to indicate the name of the archive file.                      |
| **-C** | \- | location      | \- | indicates where the archive is to be extracted.                       |

``` console
$ tar -cvf /root/etc.tar /etc
```

The above command creates the `etc.tar` archive in the `/root` directory
and puts the contents of `/etc` in that archive. Note that the file
`etc.tar` has a `.tar` extension only because we provided it, and not
because Linux mandates it (unlike windows). Thus, sometimes we may run
across tar archives that don’t have an extension and are hard to detect.
So, in that case we use the file command, which tells us the type of a
particular file.

``` console
$ file /root/etc.tar
/root/etc.tar: POSIX tar archive (GNU)
```

Note that the .tar archive only puts all the files of teh `/etc`
directory in the file `tar.etc`, but doesn’t actually compress anything.
To enable compression the `-z` option of the `tar` command must be used.

``` console
$ tar -czf /root/etc2.tgz /etc
```

Before extracting the contents of a tar file, we might want to see its
contents, which can be done using the `-t` option of the `tar` command.
*NOTE: Some older versions of* `tar` *may require the -z option to
enable working with gzip archives, even when simply using the archive
and not creating it.*

``` console
$ tar -tvf /root/etc2.tgz
```

To actually extract the archive, we use `-x` option. To indicate the
location where we want the extracted files to reside, we include the
`-C` option. If this option is not present then the files will be
extracted in the present directory.

``` console
$ tar -xvf /root/etc2.tgz -C /tmp
```

To extract only one file from the archive, we can simply provide the
name of the file at the very end.

``` console
$ tar -xvf /root/etc2.tgz -C / etc/wgetrc
```

*NOTE that in the above command, we use the relative path* `etc/wgetrc`
*because of the fact that the archive stores a relative file path for
easy extraction in any folder.*
