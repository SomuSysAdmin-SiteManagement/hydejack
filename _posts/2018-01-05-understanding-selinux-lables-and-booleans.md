---
layout : post
title : 'Understanding SELinux Lables and Booleans'
tags : [RHCSA, RHCSA_mod, Managing SELinux, Understanding SELinux Lables and Booleans]
categories: [RHCSA]
---


To manage SELinux, we need to be able to manage context. The context of
the *httpd* process can be viewed with:

``` console
# ps Zaux | grep httpd
system_u:system_r:httpd_t:s0    root       1249  0.1  0.1 226240  5156 ?        Ss   10:32   0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache     1435  0.0  0.0 228324  3160 ?        S    10:32   0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache     1436  0.0  0.0 228324  3160 ?        S    10:32   0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache     1438  0.0  0.0 228324  3160 ?        S    10:32   0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache     1441  0.0  0.0 228324  3160 ?        S    10:32   0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache     1443  0.0  0.0 228324  3160 ?        S    10:32   0:00 /usr/sbin/httpd -DFOREGROUND
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 root 2738 0.0  0.0 112664 968 pts/0 S+ 10:34   0:00 grep --color=auto httpd
```

Here we can see that the current context of the httpd process is
*httpd\_t*. Now, the default document root for the httpd process is
`/var/www` and we can see its context using:

``` console
# ls -Z /var/www
drwxr-xr-x. root root system_u:object_r:httpd_sys_script_exec_t:s0 cgi-bin
drwxr-xr-x. root root system_u:object_r:httpd_sys_content_t:s0 html
```

We can see that the context for `/var/www` has been set correctly. The
policy will state that the source context *httpd\_t* is allowed to get
through the target context *httpd\_sys\_content\_t*.

Now if an attacker finds a vulnerability on a web server script, and
tries to access the `/tmp` directory, SELinux would prevent that because
the context of `/tmp` has been set to:

``` console
# ls -dZ /tmp
drwxrwxrwt. root root system_u:object_r:tmp_t:s0       /tmp
```

Thus, the process with the source context *httpd\_t* won’t be allowed to
access a directory with a target context of *tmp\_t*.

There are primarily two situations where administrators may need to
manage context:

  - A file has been moved instead of copied, or

  - We want to do something that doesn’t correspond to the defaults.

## File being moved instead of copied

Let us consider a file *myFile* in our home directory. In that case,
it’d have the context of:

``` console
# touch myFile
# ls -Z
-rw-------. root root system_u:object_r:admin_home_t:s0 anaconda-ks.cfg
-rw-r--r--. root root system_u:object_r:admin_home_t:s0 initial-setup-ks.cfg
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 myFile
# ls -dZ
dr-xr-x---. root root system_u:object_r:admin_home_t:s0 .
```

We can see that the file we created has a context of `admin_home_t`.
This is because the current directory `/root` also has a context of
`admin_home_t`.

Now, let us make a copy of the `/etc/hosts` file and name it
`/etc/hosts2`. If we move that file, instead of copy it to the home
directory of the home user, it’ll have a context of:

``` console
# ls -Z hosts2
-rw-r--r--. root root unconfined_u:object_r:etc_t:s0   hosts2
```

The context of *hosts2* is set to *etc\_t* because while moving a file,
the original context moves with it. When copying a file, however, a new
file is created and it normally inherits the context of the parent
(target) directory.

## semanage

The **semanage** utility is used to set context. It works with a set of
arguments, and a specific argument defines what it’s actions will be.
Some of the important arguments are:

<span>lM<span>0.84</span></span> **Options** &**Description**  
**fcontext** \&Manages the fcontext of the object.  
**boolean** \&Used to change the value of a boolean  
**port** \&Changes the port type definition.  

The documentation for `semanage` has been arranged in such a way that a
separate man page exists for each of the arguments. Thus, there’s a man
page for `man semanage-fcontext`, `man semanage-boolean`, etc. The
examples in the man page for *semanage-fcontext* has examples for
setting the context for everything under the `/web` directory:

``` console
# semanage fcontext -a -t httpd_sys_content_t "/web(/.*)?"
```

The `-t` flag sets the type of `httpd_sys_content_t` for all items that
match the regular expression `/web(/.*)?`. This matches everything in
the web directory, and any files/sub-directories contained within it.

Note that `semanage fcontext` doesn’t write to the file system directly,
but to the policy. This is because all the default policies should be
set in the policy and not the file system. To apply these changes from
the policy to the file system, we need to use the command:

``` console
# restorecon -R -v /web
```

The `-R` flag makes it recursive and the `-v` flag makes it verbose. The
`restorecon` utility is also very useful when something goes wrong with
a context, because it checks the policy and ensures that the context of
every file in a directory matches their context as described in the
policy.

The file we moved from the `/etc/hosts` directory has the wrong context
of *etc\_t*, instead of *admin\_home\_t*. This can be fixed using:

``` console
# restorecon -v hosts2
restorecon reset /root/hosts2 context unconfined_u:object_r:etc_t:s0->unconfined_u:object_r:admin_home_t:s0
# ls -Z hosts2
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 hosts2
```

We can see that the file *hosts2* now has the correct context for the
directory `/root`. This could also have been done directly on the
`/root` directory to fix all the wrong contexts in the directory at
once, using `restorecon -R -v /root`.
