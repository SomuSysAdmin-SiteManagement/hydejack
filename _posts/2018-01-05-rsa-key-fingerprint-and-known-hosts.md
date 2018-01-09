---
layout : post
title : 'RSA Key fingerprint and known hosts'
tags : [RHCSA, RHCSA_mod, Connecting to a RHEL Server, RSA Key fingerprint and known hosts]
categories: [RHCSA]
---


Upon each new connection the ssh daemon shows us the RSA key fingerprint
of the host to verify if we’re connecting to the right computer. If so,
the host is added to a list of known hosts permanently, in
`~/.ssh/known_hosts`.

When the key fingerprint of the server doesn’t match the Key fingerprint
on record, the system warns us from connecting\! This may occur when the
server has been reinstalled on the same IP address. Thus, the new key
fingerprint won’t match the old one. To fix this, simply remove the old
entry from `~/.ssh/known_hosts`.
