---
layout: post
title:  "Linux Black Technology"
date:   2016-05-20 21:39:26 +0800
excerpt: "使用Linux系统过程中用到的一些功能和命令（强行黑科技23333）"
tags: [topic=linux, language=chinese, origin=true]
---

### PDF合并分割
```
pdftk 123.pdf 456.pdf output 123456.pdf
pdftk *.pdf output 123456.pdf
pdftk A=123.pdf B=456.pdf cat A1-2 B4-5 output 1245.pdf
pdftk 123.pdf output 123fixed.pdf
pdftk A=123.pdf 456.pdf input_pw A=password cat output 123456.pdf
```

### Debian使用PPA
```
sudo gedit /etc/apt/sources.list
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 949045F5
```

### 设置Grub2等待时间
```
sudo gedit /etc/default/grub
sudo gedit /etc/default/grub
```
如果要设成0的话，打开/etc/grub.d/30_os-prober文件，找到以下代码
```
set timeout_style=menu
if [ "\${timeout}" = 0 ]; then
  set timeout=10
fi
```
将10修改成0即可
