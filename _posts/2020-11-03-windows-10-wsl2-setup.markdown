---
layout: post
title:  "Windows 10 WSL2 Setup"
date:   2020-11-03 11:51:37 +0800
categories: WSL2
---

## WSL2 Config

WSL2 -> Localhost IP = `cat /etc/resolv.conf`

WSL2 Config Details -> https://www.bleepingcomputer.com/news/microsoft/windows-10-wsl2-now-allows-you-to-configure-global-options/

C:\Users\Mine\.wslconfig
```
[wsl2]
memory=8GB
swap=0
localhostForwarding=true
```

/etc/wsl.conf
```
[automount]
root = /
```


## Add sudo user group
`usermod -aG sudo mine`


## Install SapMachine JDK from https://sap.github.io/SapMachine/ & Maven from https://maven.apache.org/download.cgi

```
sudo apt-get install maven

export JAVA_HOME=/usr/lib/jvm/sapmachine-11
export PATH="$PATH:$JAVA_HOME/bin"

export M2_HOME=/usr/share/maven/
export M2=$M2_HOME/bin
```

## WSL2 SSH Setup

```
sudo apt-get install ssh
sudo apt-get install openssh-server
```

### Change default ssh settings

`sudo vim /etc/ssh/sshd_config`

### Insert: 
```
Port 22
PasswordAuthentication yes
PermitRootLogin yes -> 是否開放 root 登入
```

### Restart service after saved
`sudo /etc/init.d/ssh restart`

## Install Nodejs 10.x

```
sudo apt update
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
curl -sL https://deb.nodesource.com/setup_10.x | sudo bash
```


