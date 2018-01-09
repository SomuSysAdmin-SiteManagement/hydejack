---
layout : post
title : 'Managing Basic Permissions'
tags : [RHCSA, RHCSA_mod, Managing Permissions, Managing Basic Permissions]
categories: [RHCSA]
---


## chmod

The chmod command is used to change the permissions for a file/directory
in linux. The user is represented by the letter *u*, the group by the
letter *g* and others by *o*. The permissions themselves are represented
by:

| *Permission* |    | **Value** |
| :----------: | :-: | :-------: |
|    **r**     | \= |     4     |
|    **w**     | \= |     2     |
|    **x**     | \= |     1     |

In *absolute mode*, the individual permissions are added for each
category of owner (r/g/o) and then provided to the `chmod` command to
alter the permissions. Each category receives a value from the following
table, representing a set of permissions.

| **Value** | **Permissions**       |       | *Breakdown* |
| :-------: | :-------------------- | :---: | ----------: |
|   **7**   | Read, Write & Execute | `rwx` |     (4+2+1) |
|   **6**   | Read & Write          | `rw-` |       (4+2) |
|   **5**   | Read & Execute        | `r-x` |       (4+1) |
|   **4**   | Read only             | `r--` |         (4) |
|   **3**   | Write & Execute       | `-wx` |       (2+1) |
|   **2**   | Write only            | `-w-` |         (2) |
|   **1**   | Execute only          | `--x` |         (1) |
|   **0**   | None                  | `---` |         (0) |

So, the syntax of `chmod` becomes: `chmod <val> <filename>`. An
alternative method of applying permissions (called *relative mode*) is
directly adding or subtracting permissions in the format:

`chmod u<+-><rwx>,g<+-><rwx>,o<+-><rwx> <file-name>`

``` console
$ chmod 750 myFile
$ chmod u+x,g-r,o-wx myFile2
$ chmod 0-x myFile3
```

Now, in our example, we want the HoD to have all permissions, the group
to have rw permissions and others to have no access. Then we can set it
using:

``` console
# ls -l
total 0
drwxr-xr-x. 2 lori account 6 Nov 21 14:50 account
drwxr-xr-x. 2 lisa sales   6 Nov 21 14:50 sales
# chmod 760 account
# chmod g+w-x,o-rx sales
# ls -l
total 0
drwxrw----. 2 lori account 6 Nov 21 14:50 account
drwxrw----. 2 lisa sales   6 Nov 21 14:50 sales
```

The permissions can also be set at once using `chmod 760 account sales`.
