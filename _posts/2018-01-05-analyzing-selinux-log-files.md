---
layout : post
title : 'Analyzing SELinux Log Files'
tags : [RHCSA, RHCSA_mod, Managing SELinux, Analyzing SELinux Log Files]
categories: [RHCSA]
---


Understanding what is going wrong in a SELinux enabled environment isn’t
always easy, even though SELinux logs each occurrence of requests coming
to it. To help us there are the **setroubleshoot** packages. Whether
they’re installed or not can be checked with:

``` console
# yum list installed | grep setrouble
setroubleshoot.x86_64                      3.2.28-3.el7                @anaconda
setroubleshoot-plugins.noarch              3.0.65-1.el7                @anaconda
setroubleshoot-server.x86_64               3.2.28-3.el7                @anaconda
```

All the events that have been logged by SELinux go to the *audit log*.
In order for the audit log to be working, the *auditd* process needs to
be started. We can confirm that it’s working using `systemctl status
auditd`, and if it is, we can view the log using:

``` console
# grep AVC /var/log/audit/audit.log
type=AVC msg=audit(1513680230.189:22): avc:  denied  { write } for  pid=709 comm="accounts-daemon" name="root" dev="dm-0" ino=33574977 scontext=system_u:system_r:accountsd_t:s0 tcontext=system_u:object_r:admin_home_t:s0 tclass=dir
type=USER_AVC msg=audit(1513760499.935:10): pid=1 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:init_t:s0 msg='avc:  received setenforce notice (enforcing=0)  exe="/usr/lib/systemd/systemd" sauid=0 hostname=? addr=? terminal=?'
...
type=USER_AVC msg=audit(1513848001.387:320): pid=1 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:init_t:s0 msg='avc:  received policyload notice (seqno=2)  exe="/usr/lib/systemd/systemd" sauid=0 hostname=? addr=? terminal=?'
type=USER_AVC msg=audit(1513848601.536:329): pid=1 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:init_t:s0 msg='avc:  received policyload notice (seqno=3)  exe="/usr/lib/systemd/systemd" sauid=0 hostname=? addr=? terminal=?'
```

All SELinux messages start with the header **AVC**. Once such case where
some action was denied by SELinux is:

``` console
# grep 'type=AVC' /var/log/audit/audit.log
type=AVC msg=audit(1513680230.189:22): avc:  denied  { write } for  pid=709 comm="accounts-daemon" name="root" dev="dm-0" ino=33574977 scontext=system_u:system_r:accountsd_t:s0 tcontext=system_u:object_r:admin_home_t:s0 tclass=dir
```

The above incident tells us a file write system call was denied by
SELinux on the directory `/root` as the context noted in the policy
(*accountsd\_t*) didn’t match the context for the directory being
accessed (*admin\_home\_t*). In the `/var/log/messages` file, more
detail can be found on the event. If we check the `/var/log/messages`
file, we can see the corresponding entry in it by searching for the term
**sealert**:

``` console
# less /var/log/messages
Dec 19 16:13:56 vmPrime setroubleshoot: SELinux is preventing /usr/libexec/accounts-daemon from write access on the directory root. For complete SELinux messages run: sealert -l e277d205-f3b0-4ef7-a6c2-178a813da2e0
```

Finally, the noted command, `sealert -l
e277d205-f3b0-4ef7-a6c2-178a813da2e0` explains the event in very great
detail. **sealert** consults a database on the system to analyse what
went
wrong.

``` console
SELinux is preventing /usr/libexec/accounts-daemon from write access on the directory root.
*****  Plugin catchall (100. confidence) suggests   **************************
...
Additional Information:
Source Context                system_u:system_r:accountsd_t:s0
Target Context                system_u:object_r:admin_home_t:s0
Target Objects                root [ dir ]
Source                        accounts-daemon
Source Path                   /usr/libexec/accounts-daemon
Port                          <Unknown>
Host                          vmPrime.somuVMnet.com
Source RPM Packages           accountsservice-0.6.45-2.el7.x86_64
Target RPM Packages           filesystem-3.2-21.el7.x86_64
Policy RPM                    selinux-policy-3.13.1-166.el7.noarch
Selinux Enabled               True
Policy Type                   targeted
Enforcing Mode                Enforcing
Host Name                     vmPrime.somuVMnet.com
Platform                      Linux vmPrime.somuVMnet.com 3.10.0-693.el7.x86_64
.#1 SMP Tue Aug 22 21:09:27 UTC 2017 x86_64 x86_64
Alert Count                   1
First Seen                    2017-12-19 16:13:50 IST
Last Seen                     2017-12-19 16:13:50 IST
Local ID                      e277d205-f3b0-4ef7-a6c2-178a813da2e0
...
```

The confidence score suggests how likely a suggestion is to work. Note
that these are automated attempts to solve whatever is wrong, and might
not always be correct, and the SysAdmin must consider if it’s a valid
option and if the solution meets his/her requirements.
