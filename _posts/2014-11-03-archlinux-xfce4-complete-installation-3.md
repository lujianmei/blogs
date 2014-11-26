---
layout: post-linux
title: Archlinux 2014 + Xfce4完整安装(三)
description: "从头开始安装archlinux 2014-11-20 版本， 使用xfce做为桌面应用，并完整配置显卡驱动，声卡驱动，有线及无线网络配置"
category: linux
tags: [Archlinux2014, xfce4, 显卡驱动, 声卡驱动, 有线网络配置, 无线网络配置]
---

[Archlinux 2014](https://www.archlinux.org/) is a linux distribution whis is a lightweight and flexible Linux® distribution that tries to Keep It Simple. This is a document that complete install and config all base function from the beginning.

### 桌面环境介绍

安装桌面程序前需要先明白几个模块：
* 显示服务：
linux图形界面的运行都是采用c/s架构组成，即显示通过后端的服务的方式运行；当前有两个：X11与Wayland，X11诞生于1987年，而Wayland是当前新的桌面环境，还不是很成熟，因此现在使用的依然是X11做为界面服务提供；
而Xorg即是它的开源实现，因此`xorg-server`负责提供产品管理系统服务端，安装所有桌面都需要安装它
 
* 显卡驱动与键盘鼠标驱动：
显示成桌面后，需要支持显卡与键盘鼠标操作，因此这几个驱动是必须的，而如果是笔记本的话，可以再加上笔记本的触摸板驱动；
 
* 桌面环境
Xorg只提供图形环境的基本框架，完整的用户体验还需要其他组件。 桌面环境(DE): 在X之上并与其共同运作，提供完整的功能和动态图形界面。桌面环境通常提供图标、小程序（applets）、窗口、工具栏、文件夹、壁纸、应用程序和拖放等功能。使用GNOME、KDE、LXDE、Xfce这类桌面环境。
安装完成后需要对xinitrc文件进行相应的配置，来达到启动对应的桌面环境的目的；
 
* 显示管理器
显示管理器是用于在操作系统启动时，能自动启动对应的桌面环境，如`lightDM/slim for xfce, XDM for xorg, GDM for GNOME, KDM for KDE`
当然如果不安装显示管理器，也可以通过命令启动操作系统的桌面环境，如：
手动在命令行输入：`startx`则可以启动，而startx启动是去检查`~/.xinitrc`或者`/etc/X11/xinit/xinitrc`文件里的启动脚本；
 
* 窗口管理器
一般安装了桌面环境的会自带一个默认窗口管理器，对应各个桌面环境所使用的默认窗口管理器，可以参照：
[Desktop_Environment](https://wiki.archlinux.org/index.php/Desktop_Environment_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
当然如果不想使用大的如GNOME或KDE这种大型桌面环境，可以仅仅安装窗口管理器即可，而窗口管理器则分为动态窗口管理器，堆叠式窗口管理器，平铺式窗口管理器。
可以根据自己的需要进行选择性安装；
安装完成后，通过与显示服务进行配置，来达到启动的目的；
 

### 桌面环境安装
上面的部分界面完成后，下面开始具体对桌面环境进行安装，这里选择xfce4来采用桌面环境，因此不再需要单独安装窗口管理器，而且将显示管理器放在最后安装，刚开始可以先通过startx命令进行桌面环境的启动；
* 安装显示服务：
{% highlight css %}
pacman -S xorg-server xorg-server-utils xorg-utils mesa xorg-xinit；
{% endhighlight %}
如果是virtualbox虚拟机，还需要安装virtualbox-guest-utils
{% highlight css %}
pacman -S virtualbox-guest-utils
modprobe -a vboxguest vboxsf vboxvideo
{% endhighlight %}

* 显卡驱动与键盘鼠标驱动：
安装显卡驱动，先查询是属于什么驱动：
{% highlight css %}
lspci |grep VGA
{% endhighlight %}
然后根据对应的型号来选择驱动，可以查询驱动名称
{% highlight css %}
pacman -Ss xf86-video|less
{% endhighlight %}
而安装支持大部分显呀的通用驱动为
{% highlight css %}
pacman -S xf86-video-vesa
{% endhighlight %}
键盘鼠标，鼠标驱动
{% highlight css %}
pacman -Ss xf86-input|less
pacman -S xf86-input-keyboard
pacman -S xf86-input-mouse
pacman -S xf86-input-evdev
{% endhighlight %}

* 在安装桌面环境前，可以先安装使用默认xinitrc中的配置的窗口管理器，它是一个轻量级的叠加式窗口管理器，测试一下xorg的运行情况
{% highlight css %}
pacman -S xorg-twm xorg-xclock xterm
{% endhighlight %}
reboot重启操作系统
然后命令行登录成功后，输入`startx`则可以进入twm桌面管理器；
见效果图；

* 为了便于大家理解窗口管理器与桌面环境的区别，而且清楚其中的配置方式，我们在安装桌面环境前，再安装一个窗口管理器：openbox，然后配置出来给大家看看它的安装配置更换方式；
{% highlight css %}
pacman -S openbox
{% endhighlight %}
安装完成后，在`/etc/X11/xinit/xinitrc`文件后面加入启动`openbox`命令：
{% highlight css %}
exec openbox-session
{% endhighlight %}
或者直接通过`xinit /usr/bin/openbox-session`来启动`openbox`桌面管理器；
当启动失败时，可以在`/var/log/Xorg.0.log`中仔细检查问题并解决；

* 安装桌面环境：
安装`xfce4`，加上官网wiki建议的安装`gamin`
{% highlight css %}
pacman -S xfce4 gamin
{% endhighlight %}
启动xfce4有两种方法：
a. 安装完成后直接输入`startxfce4`命令进行启动；
b. 配置xfce4的initrc启动
{% highlight css %}
cp /etc/X11/xinit/xinitrc ~/.xinitrc
{% endhighlight %}
并在后面加上`exec startxfce4`

内容大概为：
{% highlight css linenos %}
#!/bin/sh
 
if [ -d /etc/X11/xinit/xinitrc.d ]; then
  for f in /etc/X11/xinit/xinitrc.d/*; do
    [ -x "$f" ] && . "$f"
  done
  unset f
fi
 
exec startxfce4
{% endhighlight %}
启动完成，见默认效果图：
![Smithsonian Image]({{ site.url }}/images/3953273590_704e3899d5_m.jpg)
 
当启动失败时，依然可以在`/var/log/Xorg.0.log`中仔细检查问题并解决；






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









