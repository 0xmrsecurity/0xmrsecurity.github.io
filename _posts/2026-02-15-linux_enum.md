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
ip route show
```

#### More thinks Coming soon...
