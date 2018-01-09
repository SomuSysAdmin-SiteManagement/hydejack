---
layout : post
title : 'Managing Password properties'
tags : [RHCSA, RHCSA_mod, Managing Users and Groups, User and Group configuration files]
categories: [RHCSA]
---


The user *root* can manage the password properties using two
commands:

## passwd

| **Option** | **Description**                                                                |
| :--------: | :----------------------------------------------------------------------------- |
|   **-d**   | Delete the current password.                                                   |
|   **-l**   | Lock the current password.                                                     |
|   **-u**   | Unlock the current password.                                                   |
|   **-e**   | Expire the current password - force user to change password during next login. |
|   **-x**   | Set the maximum lifetime of the password.                                      |
|   **-n**   | Set the maximum lifetime of the password.                                      |
|   **-w**   | Set days before expiration the user is warned.                                 |
|   **-i**   | Set days after expiration the user account becomes inactive.                   |

### Locking and Unlocking passwords

``` console
$ sudo passwd -l laura
Locking password for user laura.
passwd: Success
$ su - laura
Password:
su: Authentication failure
$ sudo cat /etc/shadow | grep laura
laura:!!$6$0zDhsJet$q2...KRVKv8D2.:17486:0:99999:7::17531:
$ sudo passwd -u laura
Unlocking password for user laura.
passwd: Success
$ sudo cat /etc/shadow | grep laura
laura:$6$0zDhsJet$q2...KRVKv8D2.:17486:0:99999:7::17531:
$ su - laura
Password:
Last login: Thu Nov 16 13:40:45 IST 2017 on pts/0
$ whoami
laura
```

When an account is locked, the password hash for that user in the
`/etc/shadow` file is prefixed with a `!!` to render it invalid and
prevent authentication from succeeding (unless the root logs in as that
user, which requires no password
prompt).

## chage

| **Option** | **Description**                                              |
| :--------: | :----------------------------------------------------------- |
|   **-l**   | List all password aging information.                         |
|   **-E**   | Set the account expiration date.                             |
|   **-m**   | Set the maximum lifetime of the password.                    |
|   **-M**   | Set the maximum lifetime of the password.                    |
|   **-W**   | Set days before expiration the user is warned.               |
|   **-I**   | Set days after expiration the user account becomes inactive. |

### Setting the account expiration date

``` console
$ sudo chage -E 2017-12-31 laura
[sudo] password for somu:
[somu@cliServer ~]$ sudo cat /etc/shadow | grep laura
laura:$6$0zDhsJet$q2...KRVKv8D2.:17486:0:99999:7::17531:
$ sudo chage -l laura
Last password change                              : Nov 16, 2017
Password expires                                  : never
Password inactive                                 : never
Account expires                                   : Dec 31, 2017
Minimum number of days between password change    : 0
Maximum number of days between password change    : 99999
Number of days of warning before password expires : 7
```

The string *17531* represents the account expiration date in epoch time
(seconds since Jan 1 1970).
