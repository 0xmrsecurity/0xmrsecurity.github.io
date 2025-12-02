---
title: "Linux Hunt Show"
date: 2025-11-08 00:00:00 +0800
categories: [Linux]
tags: [Linux, Hunting]
platform: Linux
author: 0xmr
image: /assets/img/posts/linux_hunt.jpeg
excerpt: "Technical understanding and practical overview"
---

Linux Hunt Show 🔍
Quick shortcuts to find critical files and information in Linux systems.

## Intersting Files
`/etc/passwd`  —> store Usersname of the box.

`/etc/shadow` —> store the Password hashes in Bcrypt form (it think)

`/etc/krb5.keytab` —→ it store the user credential in keytab formate, after we can extract the keytab file and use the `kinit` tool to create a `kcache`.

```bash
python3 keytabextract.py 'keytab file here'
```

`/etc/krb5.conf` —>it store the krb5 config (like `Domain Name` or `FQDN`)

`/etc/apache2/sites-enabled/000-default.conf` —> apache2 config file.

`/home/<username>/.ssh/id_rsa`  —> it store the public key.

`/home/<username>/.ssh/authorized_keys` —> crucial for creating Backdoors.

`bash_history` —> store the terminal history

`/proc/self/environ` -→ Current process environment variables

`/proc/[pid]/cmdline` -→ Command line of running process
`/proc/self/cmdline` -→ Command line of running process

### Search for credentials in files
```bash
grep -riE "(password|passwd|pwd|user|username|api_key|secret|token).*=" /etc /var/www /opt 2>/dev/null | grep -v "Binary"
```

## Intersting Strings Search
```bash
for file in $(find / -type f \( -name "*.cnf" -o -name "*.conf" -o -name "*.config" -o -name "*.ini" \) 2>/dev/null | grep -vE "(doc|lib|proc|sys|fonts|share|snap)"); do results=$(grep -iE "(user|username|password|pass|passwd|pwd|auth|credential|token|secret|key|api)" "$file" 2>/dev/null | grep -vE "^\s*(#|;|//)"); if [ -n "$results" ]; then echo -e "\n=== $file ==="; echo "$results" | sed 's/^/  /'; fi; done
```
```bash
find / -exec ls -lad $PWD/* "{}" 2>/dev/null \; | grep -i -I "passw\|pwd"
grep --color=auto -rnw '/' -iIe "PASSW\|PASSWD\|PASSWORD\|PWD" --color=always 2>/dev/null
```

## Intersting Database Check
```bash
(systemctl list-units --type=service; ss -tulnp) 2>/dev/null | grep -Ei 'mysql|mariadb|postgresql|mssql|3306|5432|1433'
```
```bash
for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";done
```

## Intersting Config File Check
```bash
find / -type f \( -name "*.conf" -o -name "*.config" -o -name "*.cfg" -o -name "*.ini" -o -name "*.yaml" -o -name "*.yml" -o -name "*.json" -o -name ".*rc" \) 2>/dev/null | grep -vE "^/(proc|sys|dev|run)" | sort
```
```bash
for l in $(echo ".conf .config .cnf .cfg .yaml .yml .json");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -vE "lib\|fonts\|share\|core|run\|dev\|sys\|proc" ;done
```

## Intersting Brower config file
```bash
find ~ -type f \( -name "logins.json" -o -name "Login Data" -o -name "key*.db" -o -name "signons.sqlite" \) 2>/dev/null
```
