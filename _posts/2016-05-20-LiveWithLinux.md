---
layout: post
title:  "Live with Linux"
date:   2016-05-20 21:39:26 +0800
excerpt: "使用Linux系统过程中用到的一些功能和命令，记在这里免得以后自己忘了还要重新上网查"
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

### 系统启动时自动执行脚本
/etc/rc.local这个文件是记录由用户定义的开机自动执行的命令，

### Laptop Mode
```
sudo apt-get install laptop-mode-tools
```
/etc/laptop-mode/laptop-mode.conf里可设置AC供电是否启动laptop-mode

1. **自动保存笔记本屏幕亮度**  
编辑/etc/laptop-mode/conf.d/lcd-brightness.conf做如下设定：
  + CONTROL_BRIGHTNESS=1
  + BATT_BRIGHTNESS_COMMAND="echo [value]"
  + LM_AC_BRIGHTNESS_COMMAND="echo [value]"
  + NOLM_AC_BRIGHTNESS_COMMAND="echo [value]"

2. **设置CPU调速器**  
编辑/etc/laptop-mode/conf.d/cpufreq.conf做如下设定：
  + BATT_CPU_MAXFREQ=slowest
  + BATT_CPU_GOVERNOR=powersave
  + LM_AC_CPU_GOVERNOR=performance
  + NOLM_AC_CPU_GOVERNOR=performance

### 查看CPU实时频率
```
sudo apt-get install i7z i7z-gui
sudo i7z-gui
#or "sudo i7z" for cli
```

### Shell生成随机数
```
echo $((RANDOM%200+100))
```
生成100～200之间的随机数  
PS:%是整除

### ImageMagick缩放图片
```
convert logo.png -resize 72x72 apple-icon-72x72.png
```

### mdf转iso
```
sudo apt-get install iat
iat Himanatsu.mdf Himanatsu.iso
```
