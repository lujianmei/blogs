---
layout: post-linux
title: Archlinux 2014 + Xfce4完整安装(二)
description: "从头开始安装archlinux 2014-11-20 版本， 使用xfce做为桌面应用，并完整配置显卡驱动，声卡驱动，有线及无线网络配置"
category: linux
tags: [Archlinux2014, xfce4, 显卡驱动, 声卡驱动, 有线网络配置, 无线网络配置]
---

[Archlinux 2014](https://www.archlinux.org/) is a linux distribution whis is a lightweight and flexible Linux® distribution that tries to Keep It Simple. This is a document that complete install and config all base function from the beginning.

### 配置中文，及中文输入法fcitx


1. 添加locale中文支持：
{% highlight css %}
vi /etc/locale.gen
{% endhighlight %}
确保`en_US.UTF-8 UTF-8`与`zh_CN.UTF-8 UTF-8`取消注释
然后执行locale-gen命令
{% highlight css %}
locale-gen
{% endhighlight %}

2. 安装中文字体
{% highlight css %}
pacman -S wqy-microhei wqy-microhei-lite wqy-bitmapfont wqy-zenhei ttf-arphic-ukai ttf-arphic-uming
{% endhighlight %}

3. 安装字体管理器
{% highlight css %}
pacman -S fontconfig
{% endhighlight %}
执行命令检查当前系统中中文字体安装情况：
{% highlight css %}
fc-list : lang=zh
{% endhighlight %}
发现一个中文字体都没有，因为需要对字体进行再配置；
4. 安装输入法
{% highlight css %}
pacman -S fcitx fcitx-im fcitx-configtool fcitx-sunpinyin fcitx-table-extra kcm-fcitx fcitx-ui-light fcitx-table-extra fcitx-table-other fcitx-googlepinyi
{% endhighlight %}
安装完成后，执行fcitx命令可以启动输入法管理器；然后进行手工配置自己喜欢的输入法；
将`fcitx &`加入`~/.bash-profile`文件中；
{% highlight css %}
vi ~/.bash-profile
{% endhighlight %}
5. 终端下显示中文工具
添加全局语言定义，添加在`/etc/profile`文件中
{% highlight css %}
export LANG=en_US.UTF-8
export LANGUAGE=en_US:UTF-8
export LC_CTYPE=zh_CN.UTF-8
{% endhighlight %}




 



