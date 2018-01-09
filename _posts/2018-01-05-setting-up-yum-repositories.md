---
layout : post
title : 'Setting up Yum repositories'
tags : [RHCSA, RHCSA_mod, Managing Software, Setting up Yum repositories]
categories: [RHCSA]
---


A standard RHEL installation is hooked up to RHN (Red Hat Network), the
RedHat repository, and all patches and updates are downloaded from it.
It’s the installation source and primary repository for most packages
available on RHEL.

## yum repolist

This command shows us the list of repositories which our system is
configured to use. Unless RHN is connected to, the RHEL 7 Server can’t
use this (and other) repo commands.

## Custom Repository

To convert an existing folder to a yum repository, we need to first go
to the `/etc/yum.repos.d` directory, and then create a file named:
`repoName.repo` where *repoName* is the name of our custom repository.
It’s critical that the file name ends with `.repo` as otherwise yum
won’t be able to recognize it. The contents of the repoName.repo file
should be:

``` bash
[repoName]
name=repoName
baseurl=file:///home/somu/repo
gpgcheck=0
```

The first line is called the label. The second line defines the name of
the repository. The third line, defines the URI (Uniform Resource
Identifier) where the repository is located. If it’s on the internet,
protocols such as `ftp://` can be used, but in our case since it’s on
the local filesystem, we use the `file://` protocol. Further, the path
of the repository folder is `/home/somu/repo`, which is what the
*baseurl* is set to. The fourth line turns off the GPG file integrity
checking (not suggested for real environments).

### createrepo

A final step is to generate the indexes required by yum to use the
repository. For this, we use the `createrepo` command.

``` console
# createrepo /downloads
Spawning worker 0 with 4 pkgs
Workers Finished
Saving Primary metadata
Saving file lists metadata
Saving other metadata
Generating sqlite DBs
Sqlite DBs complete
```

Next we can check if the repo was successfully added by running `yum
repolist`.

``` console
# yum repolist
Loaded plugins: fastestmirror, langpacks
repo id                             repo name                             status
base/7/x86_64                       CentOS-7 - Base                       9,591
extras/7/x86_64                     CentOS-7 - Extras                       283
repoTestLabel                       repoTest                                  0
updates/7/x86_64                    CentOS-7 - Updates                    1,134
repolist: 11,008
```

