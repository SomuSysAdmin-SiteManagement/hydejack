---
layout : post
title : 'Understanding Memory Usage'
tags : [RHCSA, RHCSA_mod, Managing Processes, Understanding Memory Usage]
categories: [RHCSA]
---


To get an overview of the free memory available we use the command `free
-m` where `-m` stands for Mega-Bytes. *Free* is the unused physical RAM.
*Shared* refers to the memory used by shared libraries (shared by
different programs). *buff/cache* is the combined Buffer and/or cache
usage, where buffer is typically used when there’s a large amount of
data that needs to be committed to disk. *Available* is the amount of
memory available for starting new applications without swapping.

``` console
# free -m
total        used        free      shared  buff/cache   available
Mem:           1823         930         198          18         694         656
Swap:          1907           0        1907
```

**Swap** contains the unused memory pages from the RAM that have been
transfered to the Hard disk.
