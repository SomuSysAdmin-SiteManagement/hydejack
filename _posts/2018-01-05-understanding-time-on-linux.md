---
layout : post
title : 'Understanding Time on Linux'
tags : [RHCSA, RHCSA_mod, Configuring Time Services, Understanding Time on Linux]
categories: [RHCSA]
---


When the system starts up, the hardware clock is reached. From this the
system time is set. The system time is the time in software maintained
by the Operating System. The system time can be changed using:

``` console
# timedatectl set-time
```

The system can even be configured to use an NTP server to set time,
using:

``` console
# timedatectl set-ntp yes
```

This will enable the **chronyd** service. The chronyd service stores its
config file in `/etc/chrony.conf`, where we can set which NTP service we
want to use.

However, the system time isn’t automatically written to the hardware
clock, and thus, if the hardware clock’s time is significantly different
from the software time, after a reboot the changes made using
`timedatectl` will be lost. To write the system time back to the
hardware clock, we use:

``` console
# hwclock --systohc
```

