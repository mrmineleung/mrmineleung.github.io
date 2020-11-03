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
memory=6GB
swap=20GB
localhostForwarding=true
```

/etc/wsl.conf
```
[automount]
root = /
```
***

## Add sudo user group
`usermod -aG sudo mine`

***
## Install SapMachine JDK & Maven 

[SapMachine JDK][SapMachine-JDK-Link]
[Maven][Maven-Link]


```
sudo apt-get install maven

export JAVA_HOME=/usr/lib/jvm/sapmachine-11
export PATH="$PATH:$JAVA_HOME/bin"

export M2_HOME=/usr/share/maven/
export M2=$M2_HOME/bin
```
***
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

***
## Install Nodejs 10.x

```
sudo apt update
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
curl -sL https://deb.nodesource.com/setup_10.x | sudo bash
```

***
## Install Docker

```
sudo apt-get remove docker docker-engine docker.io containerd runc

sudo apt-get install \
apt-transport-https \
ca-certificates \
curl \
gnupg-agent \
software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo apt-key fingerprint 0EBFCD88

sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
bionic \
stable"

sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io

sudo service docker start

sudo docker volume create portainer_data
sudo docker run -d -p 9000:9000 --name portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```


[SapMachine-JDK-Link]: https://sap.github.io/SapMachine/
[Maven-Link]: https://maven.apache.org/download.cgi