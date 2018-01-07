---
type	:	page
title	:	Technologies
menu	:	true
order	:	5
---

Here's a list of all the cool code and software that made this site possible:

## Operating Systems
To execute every single line of code, the OS used was [CentOS 7](https://www.centos.org/). Target OS would be [Red Hat Enterprise Linux (RHEL) 7](https://access.redhat.com/products/red-hat-enterprise-linux).

![CentOS 7 Logo](https://i.imgur.com/IhHCwqR.png){:.logo.logo-wide} ![RHEL 7 Logo](https://i.imgur.com/CTCuJFn.png){:.logo.logo-wide}

## Blog
This blog is maintained on a local repository on my laptop and changes usually get pushed to the host once a day. 

### Hosting
This blog is hosted on [Github-pages](https://pages.github.io), which uses the Static Site generator [Jekyll](https://jekyllrb.com/) to turn [Markdown](https://kramdown.gettalong.org) files into static HTML pages that can be served up by your browser. This site requires **no backend** languages (like `PHP` or `ASP.NET`) while serving content to the users or databases (like `MySQL`). Insted, Github pages directly converts the markdown files (and other assets) into a static website as soon as the content is pushed on to its servers.

![Github Pages Logo](https://i.imgur.com/2B1mxuP.png){:.logo.logo-wide} ![Jekyll Logo](https://i.imgur.com/bPpv59l.png){:.logo.logo-wide} ![Markdwon Logo](https://i.imgur.com/RtKCEYc.png){:.logo.logo-wide}

### Development
The `.md` files are created and edited with [Sublime Text 3](https://www.sublimetext.com/3). A local installation of Jekyll that requires [Ruby](https://www.ruby-lang.org/en/) and [Gems](https://rubygems.org/) then converts the markdown to actual HTML files, available at localhost. When any and all adjustments have been made (and errors, if any, rectified) I finally push the pages up to the site's [Github repository](https://github.com), using [Git Bash](https://git-scm.com/downloads). 

![Sublime Text Logo](https://i.imgur.com/VF6qzFh.png){:.logo.logo-wide}
![Ruby Logo](https://i.imgur.com/OHsitML.png){:.logo.logo-wide}
![Gems Logo](https://i.imgur.com/BqaivRw.png){:.logo.logo-wide}
![Github Logo](https://i.imgur.com/YnJO0BK.png){:.logo.logo-wide}
![Git Logo](https://i.imgur.com/yhYtMud.png){:.logo.logo-wide}

### Site Template
This site is a heavily customized version of [Hydejack v6](https://qwtel.com/hydejack/), which in turn is built on [Hyde](http://hyde.getpoole.com/). 
