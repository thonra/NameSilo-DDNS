<h1 align="center">
  <a href="#">
  <img src="logo.svg" width="300px">
  </a>
  <br>
</h1>

<p align="center">
<a href="https://github.com/Charles94jp/NameSilo-DDNS/tree/python"><img src="https://img.shields.io/badge/NameSilo-DDNS-brightgreen"></a>  
<a target="_blank" href="https://opensource.org/licenses/MIT"><img src="https://img.shields.io/badge/license-MIT-_red.svg"></a>  
<a href="#python3"><img src="https://img.shields.io/badge/python-v3.8-blue"></a>
</p>

<h4 align="center"><a href="https://github.com/Charles94jp/NameSilo-DDNS/blob/python/readme.zh-CN.md">简体中文</a> | English</h3>


NameSilo DDNS is a Dynamic Domain Name System service for NameSilo for home broadband, it can automatically detect IP changes in home broadband and automatically update the resolution of the domain name.

This project has been refactored via Python3, to view the Java version please switch branches.

This program is only available for domain names purchased on NameSilo.

This program obtains the public IP address of home broadband by visiting http://202020.ip138.com/, and queries and updates the DNS status by https://www.namesilo.com/api/.

It would be the best encouragement for me to get your  ⭐ STAR.

## Features

- Simple configuration, you can set the frequency of detecting IP changes and refreshing DNS.

- With email alert function, you will be alerted when there is an abnormality in the process of the service running for a long time.

- Support multi-platform (Linux, Windows...)

## Table of Contents

- [Background](#background)
- [Install](#install)
    - [Dependencies](#dependencies)
- [Usage](#usage)
    - [Configuration](#configuration)
    - [Note](#note)
    - [Start](#start)
    - [Log](#log)
    - [Start At Boot](#start-at-boot)

## Background

At present, telecom operators assign to home broadband IP are dynamic, although the IP address is not fixed, but the good thing is that the home router can get a real public IP, so we just need to use **router NAT mapping** (need router support, set up in the management console) to access the home device in the public network. After the router mapping port 22 we can remotely connect to our home linux machine, and after mapping port 445+3389 we can use the remote desktop of Win10.

![网络拓扑图](https://raw.githubusercontent.com/Charles94jp/NameSilo-DDNS/java/Network-topology.png)

To solve the problem of changing public IP, you can purchase a domain name and use DDNS (Dynamic Domain Name Server) to resolve the domain name to your broadband's IP. This will allow you to access your home devices by accessing a fixed domain name.

To achieve this, you need a computer that is always running to run this DDNS program.


## Install

Download and use

```
git -b python clone https://github.com/Charles94jp/NameSilo-DDNS.git
```

Update

```
mv conf.json conf.json.back
git pull origin python
mv conf.json.back conf.json
```

### Dependencies


A Python3 environment is required. The httpx module also needs to be installed.

```
pip install httpx
```

## Usage

### Configuration

The conf.json file needs to be configured before starting. Only the first two configurations are necessary, the rest can be set without.

|Fields|Introduction|
|--|--|
|domain|The domain name to be updated must be a subdomain. For example, if you purchase a domain name that is bb.cc, you must build a resolution on NameSilo for a subdomain such as aa.bb.cc.| 
|key|<a target="_blank" href="https://www.namesilo.com/account/api-manager">The key generated from NameSilo</a>, after generation you need to remember and keep this key.| 
|frequency|How often do you detect changes in your ip, and only update your DNS when a change in ip occurs, in seconds.| 
|mail_host| For example, you can use Google Mail's POP/IMAP | 
|mail_port| | 
|mail_user|The login user name, which is also the email sender.| 
|mail_pass|passwd or key| 
|receivers|An array to hold the recipient's address.| 

About mail alert: Simply put, after the program is stopped unexpectedly, use mail_user to send a reminder email to receivers to avoid failing to update DNS after IP changes, resulting in inaccessibility with the domain name.

Q: Under what circumstances will the program stop unexpectedly?

A: I will avoid bugs in the coding of the program itself, but errors may occur in the api being used, such as NameSilo's api or ip138's api not being able to connect, which rarely happens.

The last five configurations are not required. Only after all five are filled in will the email alert feature be enabled.

### Note


This program can only update the DNS record of a domain name, it cannot be added, please make sure this DNS record exists for your domain name and it needs to be a sub-domain.

### Start


**Direct start**

```
python ddns.py
```

**Advanced use of Linux:**

First edit the DDNS file, change the 8th line to the path of NameSilo-DDNS project, change the 17th line to the path of python 3 executable file

```
chmod +x DDNS
# usage
./DDNS {start|stop|status|restart|force-reload}
```

Example

![](example.png)

If you want to use `DDNS` command anywhere, you can create a soft link in the `/usr/bin` directory, and note that the `ln` command should use the absolute path, such as :

```
ln -s /root/NameSilo-DDNS/DDNS /usr/bin/DDNS
```

**Windows usage:** Double-click the bat or vbs file, please check the log for the running status of the program.

### Log

<b>Linux</b>

View log files

```
ls -lh DDNS*.log*
```

Among them, `DDNS.log` is the latest log file, and the rest are `gzip` compressed archive files. When DDNS service starts, if `DDNS.log` exceeds 2M, it will trigger automatic archiving. It can store all the logs since DDNS was used.

Unpacking the archived file：

```
gunzip -N DDNS-xxx.log.gz
```

Gunzip it and read it.

<b>Windows</b>

When DDNS service starts, if `DDNS.log` exceeds 2M, the old `DDNS.log` file will be renamed to `DDNS-xxx.log.back` and will not be compressed.

### Start At Boot

<b>Linux</b>

To start at boot, only RedHat series such as CentOS 7 8 and Rocky Linux 8 are demonstrated, please write your own script for other Linux distributions.

Register DDNS as a service managed by systemctl.

First of all, follow the steps in [start](#start) to configure the DDNS file.

Then configure the DDNS.service file, modify the path of DDNS file in it, and finally run:

```
cp  ./DDNS.service  /usr/lib/systemd/system/DDNS.service
systemctl daemon-reload
systemctl enable DDNS
```

<b>Windows</b>

Add the vbs file to the Windows policy group.
