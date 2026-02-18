---
title: "Linux Hunt"
date: 2025-11-08 00:00:00 +0800
categories: [Cheatsheet]
tags: [Linux, Basic linux]
platform: Cheatsheet
author: 0xmr
image: /assets/img/posts/linux_hunt.jpeg
excerpt: "Technical understanding and practical overview"
---

# Linux Hunt Show
I create some short cuts to find the Important thinks in the linux system.

## Intersting Files
`/etc/passwd`  —> store Usersname of the box.

`/etc/shadow` —> store the Password hashes in Bcrypt form (it think)

`/etc/krb5.keytab` —→ it store the user credential in keytab formate, after we can extract the keytab file and use the `kinit` tool to create a `kcache`.

```bash
python3 keytabextract.py 'keytab file here'
```

`/etc/krb5.conf` —>it store the krb5 config (like `Domain Name` or `FQDN`)

`/etc/apache2/sites-enabled/000-default.conf` —> apache2 config file.

`/etc/nginx/sites-available/default` --> store the nginx secret detail like server name and creds.

`/home/<username>/.ssh/id_rsa`  —> it store the public key.

`/home/<username>/.ssh/authorized_keys` —> crucial for creating Backdoors.

`bash_history` —> store the terminal history

`/proc/self/environ` -→ Current process environment variables

`/proc/[pid]/cmdline` -→ Command line of running process
`/proc/self/cmdline` -→ Command line of running process

## Read to use files
```bash
/etc/hosts
/etc/hostname
/etc/resolv.conf
/etc/passwd
/etc/shadow

/proc/mounts
/proc/self/environ
/proc/self/cmdline

/home/user/.env  
/home/user/.ssh/id_rsa
/home/user/.ssh/id_ed25519     
/home/user/.ssh/authorized_keys
```
## Nginx server file
```bash
======>
nginx
/etc/nginx/nginx.conf                          # Main configuration file
/etc/nginx/sites-available/default             # Default site configuration
/etc/nginx/sites-enabled/default               # Enabled site configuration
/etc/nginx/sites-available/*                   # All available sites
/etc/nginx/sites-enabled/*                     # All enabled sites
/etc/nginx/conf.d/*                            # Additional configurations
/etc/nginx/.htpasswd                           # Basic authentication file
/etc/nginx/.htaccess                           # Access control (if enabled)
/var/www/html/index.html                       # Default web root
/var/www/*/                                    # Virtual host directories
```
## Apache server files
```bash
=====>
apache
/etc/apache2/apache2.conf                      # Main configuration file
/etc/apache2/sites-available/000-default.conf  # Default site configuration
/etc/apache2/sites-enabled/000-default.conf    # Enabled site configuration
/etc/apache2/conf-available/*                  # Available configurations
/etc/apache2/conf-enabled/*                    # Enabled configurations
/etc/apache2/.htpasswd                         # Basic authentication file
/etc/apache2/.htaccess                         # Access control
/var/www/html/                                 # Default web root
/var/www/*/                                    # Virtual host directories
/var/log/apache2/access.log                    # Access logs
/var/log/apache2/error.log                     # Error logs
```
## Active Directory files
```bash
=====>
AD
/etc/krb5.keytab                               # Kerberos keytab file (sensitive!)
/etc/krb5.conf                                 # Kerberos configuration
/etc/krb5kdc/kdc.conf                          # KDC configuration
/var/lib/krb5kdc/principal                     # Kerberos principal database
/etc/sssd/sssd.conf                            # SSSD configuration (LDAP/AD)
/etc/ldap/ldap.conf                            # LDAP configuration
/etc/openldap/ldap.conf                        # OpenLDAP configuration
=====>
```
## Strings Search ? (Passwd)
```bash
# Custom string search
grep -ri --include="*.<Extension>" -n "<String>" <Directory> 2>/dev/null

# Password string search
grep -ri --include="*.xml" -n "Password" /opt 2>/dev/null
```


## Find Config Files ?

Custom File Extensions:-
```bash
# Custom extension file search
find / -type f -name "*.<Extension Name>" 2>/dev/null
```

All Config file check:-
```bash
# Search Whole file system
find / -type f \( -name "*.xml" -o -name "*.db" -o -name "*.sql" -o -name "*.config" \) 2>/dev/null

# Specific Paths /opt /etc /home /var
find /opt /var /home /etc -type f \( -name "*.xml" -o -name "*.db" -o -name "*.sql" -o -name "*.config" \) 2>/dev/null
```


## Intersting Strings Search !!!
```bash
for file in $(find / -type f \( -name "*.cnf" -o -name "*.conf" -o -name "*.config" -o -name "*.ini" \) 2>/dev/null | grep -vE "(doc|lib|proc|sys|fonts|share|snap)"); do results=$(grep -iE "(user|username|password|pass|passwd|pwd|auth|credential|token|secret|key|api)" "$file" 2>/dev/null | grep -vE "^\s*(#|;|//)"); if [ -n "$results" ]; then echo -e "\n=== $file ==="; echo "$results" | sed 's/^/  /'; fi; done
```
```bash
find / -exec ls -lad $PWD/* "{}" 2>/dev/null \; | grep -i -I "passw\|pwd"
grep --color=auto -rnw '/' -iIe "PASSW\|PASSWD\|PASSWORD\|PWD" --color=always 2>/dev/null
```


## Find Databases ?
```bash
# Check Running Databases
(systemctl list-units --type=service; ss -tulnp) 2>/dev/null | grep -Ei 'mysql|mariadb|postgresql|mssql|3306|5432|1433'
```


## Intersting Browser config File ?
```bash
find ~ -type f \( -name "logins.json" -o -name "Login Data" -o -name "key*.db" -o -name "signons.sqlite" \) 2>/dev/null
```
