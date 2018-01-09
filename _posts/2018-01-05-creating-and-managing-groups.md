---
layout : post
title : 'Creating and Managing Groups'
tags : [RHCSA, RHCSA_mod, Managing Users and Groups, Creating and Managing Groups]
categories: [RHCSA]
---


## groupadd

The `groupadd` command is used to add a new group.

| **Option** | **Description**               |
| :--------: | :---------------------------- |
|   **-g**   | Specify the GID of the group. |

## Adding users to a group

A user can be added to a group by directly editing the `/etc/group`
file, or by| using the `moduser`
command.

### usermod

| **Option** | **Description**                                                             |
| :--------: | :-------------------------------------------------------------------------- |
|   **-g**   | Force assign the GID as the new default group of the user.                  |
|   **-G**   | Erase older list of supplementary groups and assign the given groups as the |
|            | supplementary group of he user.                                             |
|   **-a**   | Add the given group to the list of groups for the user.                     |

Adding a user to a group using the `usermod` command is shown below.

``` console
$ sudo usermod -aG account laura
$ sudo usermod -aG 5010 lisa
$ tail -n 1 /etc/group
account:x:5010:laura,lisa
```
