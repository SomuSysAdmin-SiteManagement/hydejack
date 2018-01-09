---
layout : post
title : 'Configuring a VNC Server'
tags : [RHCSA, RHCSA_mod, Configuring VNC Access, Configuring a VNC Server]
categories: [RHCSA]
---


The main difficulty in setting up a functional VNC server comes from the
fact that there are a lot of tiny components that need to work together
to make the session work. First we need to install **tigervnc** and
**tigervnc-server**. TigerVNC is the client and the other package, the
VNC server.

To use VNC, we need to specify the settings for a particular user. For
this we add a user called *vncuser*, and add a password for it, since
it’s going to connect through an SSH session.

``` console
# useradd vncuser
[root@vmPrime ~]# passwd vncuser
Changing password for user vncuser.
New password:
BAD PASSWORD: The password fails the dictionary check - it is based on a dictionary word
Retype new password:
passwd: all authentication tokens updated successfully.
```

## Creating the VNC Server Configuration File

In the `/usr/lib/systemd/system` directory, there is a file named
`vncserver@.service`. We need to copy and rename it to:

``` console
# cp vncserver@.service vncserver@\:1.service
# ls vncserver*
vncserver@:1.service  vncserver@.service
```

The `\` in the name of the file is just used as an escape character to
provide the `:` in the file name `vncserver@:1.service`. The number
\(1\) in the file name is the number of the VNC session (\(1^{st}\))
that we want to provide. The session number must start from 1 onwards,
since the service will refuse to start for session number 0. Now, we
modify the contents of the file we just created. There are sections in
the file that contain placeholders called `<USER>` which must be
replaced with the actual username that we just created:

``` bash
[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target

[Service]
Type=forking
User=vncuser    # There was a placeholder <USER> here

# Clean any existing files in /tmp/.X11-unix environment
ExecStartPre=-/usr/bin/vncserver -kill %i
ExecStart=/usr/bin/vncserver %i
PIDFile=/home/vncuser/.vnc/%H%i.pid # There was a placeholder <USER> here
ExecStop=-/usr/bin/vncserver -kill %i

[Install]
WantedBy=multi-user.target
```

Now, systemd needs to be notified that a new configuration file has been
added. We use the `systemctl daemon-reload` which causes systemd to
reload all unit files, in which our new service will also be loaded.

``` console
# systemctl daemon-reload
```

Finally, before starting the VNC server session, we must set a VNC
password for *vncuser*. Note that this can only be done as the VNC user,
and **NOT** as root, even though RedHat documentation may suggest it.

``` console
# su - vncuser
$ vncpasswd
Password:
Verify:
Would you like to enter a view-only password (y/n)? n
$ exit
logout
```

Now, the VNC environment has been setup for *vncuser*, and we can start
the VNC server.

``` console
# systemctl start vncserver@\:1
# systemctl status vncserver@\:1
● vncserver@:1.service - Remote desktop service (VNC)
Loaded: loaded (/usr/lib/systemd/system/vncserver@:1.service; disabled; vendor preset: disabled)
Active: active (running) since Fri 2017-12-22 21:38:20 IST; 26s ago
Process: 17085 ExecStart=/usr/bin/vncserver %i (code=exited, status=0/SUCCESS)
Process: 17076 ExecStartPre=/usr/bin/vncserver -kill %i (code=exited, status=2)
Main PID: 17101 (Xvnc)
CGroup: /system.slice/system-vncserver.slice/vncserver@:1.service
├─17101 /usr/bin/Xvnc :1 -auth /home/vncuser/.Xauthority -desktop vmPrime.somuVMnet.com:1 (vncuser) -fp catalogue:/etc/X11/fontpath.d -geometry 1024x768 -pn -rfbauth /home/vncuser...
├─17106 /usr/libexec/gnome-session-binary --session=gnome-classic
...
```

Now we need to allow the service through the firewall. Firewalls in
generals are more permissive for outgoing connections than incoming
connections.

``` console
# firewall-cmd --permanent --add-service=vnc-server
success
# firewall-cmd --reload
```

The last command, `firewall-cmd --reload` causes firewalld to reload
it’s configuration. This is all the setup needed on the VNC server.

Thus the steps to setup a VNC server can be boiled down to:

![Steps for VNC Server
config<span label="fig:4 Steps for VNC Server config"></span>](RHCSA/Mod4/chapters/4.26.b)
