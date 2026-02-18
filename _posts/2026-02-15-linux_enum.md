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
> 

# System & Identity
```bash
id                        # User’s identity information
hostname or hostnamectl   # Host Name of Computer
whoami                    # who are you
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
px -ef --forest
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

# SUID Files
```bash
# Special file permissions in Linux that allow users to execute files with the permissions of the file's owner.
find / -type f -perm -4000 2>/dev/null

find / -perm -u=s -type f 2>/dev/null | grep -i 'Username Here'
find / -type f -perm -4000 -user <Username Here> 2>/dev/null

find / -perm -4000 -type f -exec ls -ld {} \\; 2>/dev/null   
find / -perm -u=s -type f -exec ls -la {} + 2>/dev/null
```

# Capabilities
```bash
getcap -r / 2>/dev/null 
```

#### More thinks Coming soon...
