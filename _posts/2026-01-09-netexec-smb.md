---
title: "NetExec SMB Protocal"
date: 2026-01-10 00:00:00 +0530
categories: [AD- Attacks]
tags: [135 445 Port, Netexec smb, smb, SMB Protocal]
platform: AD- Attacks
author: 0xmr
image: /assets/img/posts/smb.png
excerpt: "Technical understanding and practical overview"
---
# NetExec SMB
### Authentication
```bash
nxc smb $IP -u '' -p <pass> -H <NTLM_HASH> --use-kcache -k --local-auth -d <domain> --kdcHost <FQDN>

-p               # Password Auth
-H               # NTLM Hash
--user-kcache    # Kcache key
-k               # kerberos Auth

-d  or --kdcHost   # Specify Domain Name and Fully Qualified Domain Name
```
### Generate File
```bash
nxc smb $IP/24  -u '' -p ''  --gen-relay-list sign_off.txt | --generate-hosts hosts |  --generate-krb5-file krb5.conf |  --generate-tgt tgt.ccache

sudo cat sign_off.txt                  # check signing attack possible or not !       
sudo cat hosts.txt >> /etc/hosts       # fix  host name 
sudo cp krb5.conf /etc/krb5.conf       # fix  kdc error
export KRB5CCNAME=tgt.ccache           # Export ccahe key and authenticate it !
```
### Enumeration
```bash
nxc smb $IP  --users  --shares  --groups  --computers  --sessions  --rid-brute   --loggedon-users  --pass-pol  --disks

--users            # Enumerate domain users
--shares           # List all SMB shares and permissions
--groups           # Enumerate domain groups
--computers        # List domain computers
--sessions         # Show active SMB sessions/connections
--rid-brute 10000  # Brute force users via RID cycling (up to RID 10000)
--loggedon-users   # Show currently logged-on users
--pass-pol         # Display domain password policy
--disks            # Enumerate physical and logical drives attached to the system
```
# More Thinks Add soon !!!
