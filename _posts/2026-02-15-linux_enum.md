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
find / -type f -perm -4000 2>/dev/null
find / -perm -4000 -type f -ls 2>/dev/null

find / -perm -u=s -type f 2>/dev/null | grep -i 'Username Here'
find / -type f -perm -4000 -user <Username Here> 2>/dev/null

find / -type f -a \( -perm -u+s -o -perm -g+s\) -exec ls -l {} \; 2> /dev/null
```


# Capabilities
```bash
getcap -r / 2>/dev/null 
```


# SSH & .env  Files
```bash
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

# CronTab's
> Adding soon........!!!

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


# Active Directory Files
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
