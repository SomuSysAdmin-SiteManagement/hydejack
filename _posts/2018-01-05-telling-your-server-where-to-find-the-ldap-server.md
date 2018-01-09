---
layout : post
title : 'Telling your server where to find the LDAP Server'
tags : [RHCSA, RHCSA_mod, Connecting to a LDAP Server, Telling your server where to find the LDAP Server]
categories: [RHCSA]
---


## nscd

The Naming Service Cache Daemon needs to be installed to configure the
connection of a server to an external LDAP server. It is the part of the
OS that caches the information from external authentication mechanisms
on the local machine.

## nss-pam-ldapd

This sets up the local name resolution and local authentication and
connects it to LDAP services.

## pam\_ldap

The libraries needed to make the local authentication aware of LDAP
services.

## authconfig-gtk

`authconfig` is an utility used to setup the server for external
authentication. There are several variations of it, such as
`authconfig`, `authconfig-tui` and `authconfig-gtk` (GUI based).

### LDAP Search Base DN

The search base DN consists of Domain Components (dc) with commas as
separators. Example : `dc=rhatcertification.com,dc=com`.

### LDAP Server

The server needs to have a matching certificate to the one that the
client receives on connection. This is only possible with a domain name,
and not an IP address. The reason for this is the server name has to
match the one in the certificate and the certificate can only have one
name associated to it.

### TLS Certificate

The use of a Transport Layer Security (TLS) certificate is important
because unless it’s used, the LDAP password is sent across the network
unencrypted, which makes the entire system vulnerable. We also need to
download the TLS certificate from the server (e.g.,
`ftp://server.rhatcertification.com/pub/slapd.pem`).

## Switching to an LDAP user

The user can switch to an LDAP user just as easily as a local user
using:

``` console
$ su - <ldap_username>
```
