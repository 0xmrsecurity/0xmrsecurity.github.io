---
title: "Bloodhoound"
date: 2026-05-21 00:00:00 +0800
categories: [AD- Attacks]
tags: [bloodhound]
platform: AD- Attacks
author: 0xmr
image: /assets/img/posts/bloodhound.png
excerpt: "Bloodhoound..."
---

# Bloodhound 
> BloodHound is an open-source tool that uses graph theory to visually map and analyze the relationships and permissions between objects (like users, groups, and computers).

### Installtion Guide
```bash
cd /opt
# Bloodhound Directory Creation
mkdir bloodhound

# Installing Docker-Compose
sudo apt install docker.io && sudo apt install docker-compose
# Add user to run Docker Containers without Priv
sudo usermod -aG docker  $USER
# Installing Bloodhound File
curl -L https://ghst.ly/getbhce > docker-compose.yml

# Start Bloodhound Container
sudo docker-compose pull && docker-compose up

http://localhost:8080/

# Greb Intial Password from the log's from Screen

# Login creds
admin:<Initial_Passwords>
```

### Custom Queries

#### `List all Users`
```bash
Match (n:User) RETURN n
```
#### `List all computers`

```bash
Match (n:Computers) RETURN n
```

#### `List all Kerberoastable Users`

```bash
MATCH (n:User)WHERE n.hasspn=true
RETURN n

MATCH (n:User {hasspn: true}) WHERE NOT n.name STARTS WITH 'KRBTGT' 
RETURN n   
```

#### `List all As-Reproastable Users`
```bash
MATCH (n:User)WHERE n.dontreqpreauth=true 
RETURN n   

MATCH (n:User {dontreqpreauth: true, enabled: true}) 
RETURN n
```

## Delegation Attacks

#### `Unconstrained Delegation`
```bash
# By Computer
MATCH (n:Computer {unconstraineddelegation:true}) 
return n

# By Users
MATCH (n:User {allowedtodelegate: true}) 
RETURN n
```

#### `Constrained Delegation`
```bash
MATCH (n {allowedtodelegate: true}) 
RETURN n   
```

#### `Resource-Based Constrained Delegation`
```bash
MATCH (n:Computer) WHERE EXISTS(c.allowedtoact) 
RETURN n   
```
