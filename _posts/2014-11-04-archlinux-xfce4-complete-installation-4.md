---
layout: post-linux
title: Archlinux 2014 + Xfce4完整安装(四)
description: "从头开始安装archlinux 2014-11-20 版本， 使用xfce做为桌面应用，并完整配置显卡驱动，声卡驱动，有线及无线网络配置"
category: linux
tags: [Archlinux2014, xfce4, 显卡驱动, 声卡驱动, 有线网络配置, 无线网络配置]
---

[Archlinux 2014](https://www.archlinux.org/) is a linux distribution whis is a lightweight and flexible Linux® distribution that tries to Keep It Simple. This is a document that complete install and config all base function from the beginning.

### 配置有线网络及无线网络
* 安装有线及无线网络管理工具
{% highlight css %}
pacman -S net-tools #有线
wireless_tools, wpa_supplicant dialog #无线
{% endhighlight %}

* 安装网络界面管理工具
{% highlight css %}
pacman -S networkmanager, network-manager-applet xfce4-notifyd
{% endhighlight %}

* 添加用户到 `network` 用户组：
{% highlight css %}
gpasswd -a USERNAME network
{% endhighlight %}

* 取消 netctl,  dhcpcd 的自动启动，然后使用networkmanager来进行管理网络
{% highlight css %}
systemctl disable netctl
systemctl disable dhcpcd
systemctl enable NetworkManager
{% endhighlight %}

* 删除文件`/etc/wpa_supplicant.conf`，重新生成一下
{% highlight css %}
echo 'ctrl_interface=DIR=/run/wpa_supplicant' > /etc/wpa_supplicant.conf
{% endhighlight %}

* 将对应的无线的对应的SSID的密码生成到文件`/etc/wpa_supplicant.conf`中
{% highlight css %}
wpa_passphrase <ssid> <passphrase> >> /etc/wpa_supplicant.conf
{% endhighlight %}

### 配置声音系统
* 参照archlinux官网[声音系统](https://wiki.archlinux.org/index.php/ALSA_%E5%AE%89%E8%A3%85%E8%AE%BE%E7%BD%AE_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29)进行配置；
* 参照网站[声音系统问题排查](http://xianguo.com/article/C83D834D95BB0A6B82908598C3EE605A)进行问题解决处理


### 安装通用软件：
* 安装chrome
{% highlight css %}
pacman -S chromium
{% endhighlight %}
* 安装文件阅读器 Okular
{% highlight css %}
pacman -S okular
{% endhighlight %}
* 安装virtualbox 
{% highlight css %}
pacman -S  virtualbox-host-modules virtualbox virtualbox-host-dkms linux-headers
dkms install vboxhost/$(pacman -Q virtualbox|awk {'print $2'}|sed 's/\-.\+//') -k $(uname -rm|sed 's/\ /\//')
modprobe vboxdrv
systemctl enable dkms.service
{% endhighlight %}
* 安装thunderbird
{% highlight css %}
pacman -S thunderbird
{% endhighlight %}

* 安装文件索引和搜索
{% highlight css %}
pacman -S mlocate
{% endhighlight %}
然后 执行updatedb建立文件系统索引
然后就可以使用locate命令快速定位查找文件






