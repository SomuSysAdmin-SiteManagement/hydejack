---
layout : post
title : 'Understanding Linux File System Layout'
tags : [RHCSA, RHCSA_mod, Essential File Management Tools, Understanding Linux File System Layout]
categories: [RHCSA]
---


<table>
<thead>
<tr class="header">
<th style="text-align: left;"><strong>root (/)</strong></th>
<th style="text-align: center;">-</th>
<th style="text-align: left;">Contains all other directories.</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong>/boot</strong></p></td>
<td style="text-align: center;">-</td>
<td style="text-align: left;">Contains everything the system needs to start up</td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong>/usr</strong></p></td>
<td style="text-align: center;">-</td>
<td style="text-align: left;">Contains program files</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong>/etc</strong></p></td>
<td style="text-align: center;">-</td>
<td style="text-align: left;">Contains configuration files</td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong>/home</strong></p></td>
<td style="text-align: center;">-</td>
<td style="text-align: left;">Contains a user’s files</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong>/mnt</strong></p></td>
<td style="text-align: center;">-</td>
<td style="text-align: left;">Used to manually mount devices</td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong>/media</strong></p></td>
<td style="text-align: center;">-</td>
<td style="text-align: left;">Devices like optical discs get auto-mounted on the media directory</td>
</tr>
</tbody>
</table>

Unlike other OSs, the linux files system is designed as such that
multiple devices can be mounted on the same file system hierarchy. Thus,
it’s possible to mount devices remotely as well\!
