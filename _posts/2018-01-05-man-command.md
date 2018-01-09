---
layout : post
title : 'Man Command'
tags : [RHCSA, RHCSA_mod, Using Essential Tools, Man Command]
categories: [RHCSA]
---


**man** followed by *keyword* yields the manual page of that command.

``` console
$ man ls
```

**man** followed by option **-k** (for keyword) and then followed by a
*keyword* yields a list of all the commands containing that keywork and
a brief descripiton of that command.

``` console
$ man -k day
daylight (3)         - initialize time conversion information
dysize (3)           - get number of days for a given year
daylight (3p)        - set timezone conversion information
gettimeofday (2)     - get / set time
gettimeofday (3p)    - get the date and time
motd (5)             - message of the day
Net::Time (3pm)      - time and daytime network client interface
settimeofday (2)     - get / set time
Time::HiRes (3pm)    - High resolution alarm, sleep, gettimeofday, interval timers
```

The numbers next to the commands indicate which seciton of the man pages
the command belongs to (based on their functionality). The actual
section that the commands belong to can be determined by the use of

``` console
$ man man-pages
```

The relevant sections for SysAdmins are Section 1, 5 & 8. The sections
are:

| Section Number | Deals with                               | Description                                                                                                      |
| :------------- | :--------------------------------------- | :--------------------------------------------------------------------------------------------------------------- |
| 1              | Commands (Programs)                      | Those commands that can be executed by the user from within a shell.                                             |
| 2              | System calls                             | Those functions which must be performed by the kernel.                                                           |
| 3              | Library calls                            | Most of the libc functions.                                                                                      |
| 4              | Special files (devices)                  | Files found in /dev.                                                                                             |
| 5              | File formats and conventions             | The format for /etc/passwd and other human-readable files.                                                       |
| 6              | Games                                    |                                                                                                                  |
| 7              | Overview, conventions, and miscellaneous | Overviews of various topics, conventions and protocols, character set standards, and miscellaneous other things. |
| 8              | System management commands               | Commands like mount(8), many of which only root can execute.                                                     |

To filter down the output of the **man -k** command, we can use **grep**
to obtain only the relevant parts of the result on the basis of the
appropriate section number in the man-pages.

This can be achieved using the pipe which feeds the output of the first
command to the input of the second command.

``` console
$ man -k day | grep 3
daylight (3)         - initialize time conversion information
dysize (3)           - get number of days for a given year
daylight (3p)        - set timezone conversion information
gettimeofday (3p)    - get the date and time
Net::Time (3pm)      - time and daytime network client interface
Time::HiRes (3pm)    - High resolution alarm, sleep, gettimeofday, interval timers
```

