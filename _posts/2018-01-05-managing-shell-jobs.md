---
layout : post
title : 'Managing Shell Jobs'
tags : [RHCSA, RHCSA_mod, Managing Processes, Managing Shell Jobs]
categories: [RHCSA]
---


Every command on the shell is considered a shell job. Many of them start
and stop immediately (execute very fast) because they’ve completed their
task.

``` console
$ ls
Desktop    Downloads  Pictures  Public     Videos
Documents  Music      Programs  Templates
```

Other programs may need us to wait while they finish their jobs. In such
cases, we can put them to work in the background and not have them
obstruct our work.

``` console
$ cat test.sh
#!/bin/bash
echo "Starting"
sleep 10
echo "Completed!"
$ ./test.sh
Starting
^Z
[1]+  Stopped                 ./test.sh
$ jobs
[1]+  Stopped                 ./test.sh
$ bg
[1]+ ./test.sh &
$ Completed!

[1]+  Done                    ./test.sh
$ ./test.sh &
[1] 39773
Starting
$ Completed!

[1]+  Done                    ./test.sh
```

To directly start a job in the background, we need only suffix the
command with a `&`. If we have multiple commands running in the
background, we can put any one of them in the foreground using: `fg
<jobId>`. Similarly, to kill a job, simply type: `%<jobId>`.

``` console
$ sleep 600 &
[1] 41461
$ dd if=/dev/zero of=/dev/null
^Z
[2]+  Stopped                 dd if=/dev/zero of=/dev/null
$ jobs
[1]-  Running                 sleep 600 &
[2]+  Stopped                 dd if=/dev/zero of=/dev/null
$ bg
[2]+ dd if=/dev/zero of=/dev/null &
$ jobs
[1]-  Running                 sleep 600 &
[2]+  Running                 dd if=/dev/zero of=/dev/null &
$ fg 1
sleep 600
^C
$ fg
dd if=/dev/zero of=/dev/null
^Z
[2]+  Stopped                 dd if=/dev/zero of=/dev/null
$ jobs
[2]+  Stopped                 dd if=/dev/zero of=/dev/null
$ kill %2
[2]+  Terminated              dd if=/dev/zero of=/dev/null
$ jobs
$
```

