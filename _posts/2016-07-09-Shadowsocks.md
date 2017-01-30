---
layout: post
title:  "Shadowsocks on Linux"
date:   2016-07-09 21:39:26 +0800
excerpt: "连自己的翻墙梯子都没有那和咸鱼有什么区别"
---

**This post is a tutorial about setting Shadowsocks on Linux server and Linux client.**

****

### Install Shadowsocks on VPS

Normally, we should install shadowsocks-libev from Debian's official repository, but the package is in unstable branch while I'm using stable. So I install it from a unofficial repository.

First, add the GPG public key to your system:

```
wget -O- http://shadowsocks.org/debian/1D27208A.gpg | sudo apt-key add -
```

Install the binaries by adding each of the following repositories to your system.  
On Debian Wheezy, Ubuntu 12.04 or any distribution with libssl > 1.0.0

```
echo "deb http://shadowsocks.org/debian wheezy main" >> /etc/apt/sources.list
```

Then

```
apt-get update
apt-get install shadowsocks-libev
```

It may show you a error message for unmet dependency, depend on which distribution are you using and how many packages are there in your distribution's repositories.

### Config Shadowsocks on VPS

Edit /etc/shadowsocks/config.json to config Shadowsocks.

```
{
    "server":"my_server_ip",
    "server_port":8388,
    "local_port":1080,
    "password":"barfoo!",
    "timeout":600,
    "method":"table",
    "auth": true
}
```

+ server: your hostname or server IP (IPv4/IPv6)
+ server_port: server port number
+ local_port: local port number
+ password: a password used to encrypt transfer
+ timeout: connections timeout in seconds
+ method: encryption method, "bf-cfb", "aes-256-cfb", "des-cfb", "rc4", etc. Default is table, which is not secure. "aes-256-cfb" is recommended
+ auth: one-time authentication, set true to enable one-time authentication feature.

The command to operate Shadowsocks:

+ /etc/init.d/shadowsocks-libev start
+ /etc/init.d/shadowsocks-libev stop
+ /etc/init.d/shadowsocks-libev restart
+ /etc/init.d/shadowsocks-libev status

### Install shadowsocks-qt5 on Client

I use shadowsocks-qt5, it's a cross platform GUI software. This software isn't in Debian's repositories, so I build a deb package to install it.

Prepare the dependencies.

```
sudo apt-get install qt5-qmake qtbase5-dev libqrencode-dev libqtshadowsocks-dev libappindicator-dev libzbar-dev libbotan1.10-dev
```

libqtshadowsocks-dev isn't in Debian's repositories, build deb to install it.

```
sudo apt-get install qt5-qmake qtbase5-dev libbotan1.10-dev #skip this part if you've already installed these packages
git clone https://github.com/shadowsocks/libQtShadowsocks.git
cd libQtShadowsocks
dpkg-buildpackage -uc -us -b
```

Build shadowsocks-qt5.

```
git clone https://github.com/shadowsocks/shadowsocks-qt5.git
cd shadowsocks-qt5
dpkg-buildpackage -uc -us -b
```

### Set shadowsocks-qt5 on Client

![](http://7xt9ka.com2.z0.glb.qiniucdn.com/shadowsocks-qt5-0.png)

Connection->Add->Manually

![](http://7xt9ka.com2.z0.glb.qiniucdn.com/shadowsocks-qt5-1.png)

+ Server Address: your hostname or server IP (IPv4/IPv6)
+ Server Port: server port number
+ Password: a password used to encrypt transfer
+ Encryption Method: AES-256-CFB(same as Shadowsocks server's config on VPS)

Setting complete.

### Note

1. Even if you set shadowsocks-qt5 on your Linux PC, you can't use Google immediately. You must set your Internet browser.
2. How can I use Shadowsocks in all my system not only a browser?
3. In Linode VPS, install package 'ssserver' to for a python port shadowsocks server.
4. When use shadowsocks python port, the start command is:
```
ssserver -c /etc/shadowsocks/config.json
```
