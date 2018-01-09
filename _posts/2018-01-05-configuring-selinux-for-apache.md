---
layout : post
title : 'Configuring SELinux for Apache'
tags : [RHCSA, RHCSA_mod, Managing SELinux, Analyzing SELinux Log Files]
categories: [RHCSA]
---


This is how we deal with SELinux during real-life scenarios such as
while configuring the apache web server. Let us consider we want to use
a new document root at `/web`. We put an index.html file in the
directory, and configure the `/etc/http/conf/httpd.conf` file with the
new document root at `/web`, by adding the lines below:

``` lighttpd
DocumentRoot "/web"

<Directory "/web">
AllowOverride None
# Allow open access:
Require all granted
</Directory>

#<Directory "/var/www/html"> --> Manually commenting out the old tag start, and copying the original config with our own.
<Directory "/web">
...
```

The lines 3-7 help provide access to the new document root. Note that
this is modelled on the original `<Directory>` tag for the document root
`/var/www`, which itself must not be edited or commented out (to stop
other functionality from being disabled).

Once the *httpd.conf* file has been edited, we need to restart the
apache service, with `systemctl restart httpd`. Now, the index.html
should be available on the address  
`http://localhost`. Instead of the index.html page, we see an error page
from the apache web server that reads *"The website you just visited is
either experiencing problems or undergoing routine maintenance"*.

This generates the following SELinux notifications in the logs:

``` bash
# In /var/log/audit/audit.log: [grep AVC /var/log/audit/audit.log]
type=AVC msg=audit(1513869456.604:322): avc:  denied  { getattr } for  pid=3683 comm="httpd" path="/web/index.html" dev="dm-0" ino=2556901 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:default_t:s0 tclass=file

# In /var/log/messages: [less /var/log/messages ?sealert]
Dec 21 20:49:37 vmPrime setroubleshoot: SELinux is preventing httpd from getattr access on the file /web/index.html. For complete SELinux messages run: sealert -l 001da822-04b5-498d-8344-d78de9014597
```

It is clear from the message in the audit log that this is a case of
context type mismatch. The source has a context label of *httpd\_t*
while the target directory and file (`/web/index.html`) have the context
label of *default\_t*. We can fix this using the command:

``` console
# semanage fcontext -a -t httpd_sys_content_t '/web(/.*)?'
# restorecon -R -v /web
restorecon reset /web context unconfined_u:object_r:default_t:s0->unconfined_u:object_r:httpd_sys_content_t:s0
restorecon reset /web/index.html context unconfined_u:object_r:default_t:s0->unconfined_u:object_r:httpd_sys_content_t:s0
```

Now when we visit the webpage at `http://localhost/index.html`, SELinux
won’t block us anymore.
