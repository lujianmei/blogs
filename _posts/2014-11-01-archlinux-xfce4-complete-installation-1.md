---
layout: post-linux
title: Archlinux 2014 + Xfce4完整安装(一)
description: "从头开始安装archlinux 2014-11-20 版本， 使用xfce做为桌面应用，并完整配置显卡驱动，声卡驱动，有线及无线网络配置"
category: linux
tags: [Archlinux2014, xfce4, 显卡驱动, 声卡驱动, 有线网络配置, 无线网络配置]
---

[Archlinux 2014](https://www.archlinux.org/) is a linux distribution whis is a lightweight and flexible Linux® distribution that tries to Keep It Simple. This is a document that complete install and config all base function from the beginning.

### archlinux安装步骤命令


1. 磁盘分区：
{% highlight css %}
cfdisk /dev/sda 
{% endhighlight %}
对磁盘进行分区，如果使用grub作为启动管理器，则需要将`/boot`单独分一个区；
通常分三个盘：
`/dev/sda1` 为`/boot`, 配置500M大小分区即可
`/dev/sda5` 为`swap`, 配置内存的两倍，如果内存很大，则一倍或一半也可以，按具体电脑使用需求来配置；
`/dev/sda6` 为`/`， 剩余硬盘空间大小；
且按我个人的习惯，习惯把/boot分为主分区，而其它则分为扩展分区来使用；
2. 分区后需要对分区进行格式化：
{% highlight css %}
mkfs.btrfs /dev/sda6
mkfs.btrfs /dev/sda1
mkswap /dev/sda5
swapon /dev/sda5
{% endhighlight %}
btrfs的文件系统要优于ext文件系统，具体可以参考阅读[新一代 Linux 文件系统 btrfs 简介](http://www.ibm.com/developerworks/cn/linux/l-cn-btrfs/); 
3. 分区文件mount到当前host系统上，以用来安装新的系统到对应磁盘
{% highlight css %}
mount /dev/sda6 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
{% endhighlight %}
4. 配置安装镜像站点，以站安装速度更快
{% highlight css %}
vi /etc/pacman.d/mirrorlist
{% endhighlight %}
将163,或者选择对应国内镜像站点放前面，以提高安装包的下载速度；
在接下来的步骤就需要网络，因此这里需要提前检查一下电脑的网络是否已经配置；
通过`ifconfig`来检查网络情况，如果网络不好，可以采用`systemctl status dhcpcd`来检查自动获取网络的服务是否启动，或者通过`ifconfig eth0`（网卡名）来配置静态ip地址；
5. 安装基本操作系统
{% highlight css %}
pacstrap /mnt base base-devel
{% endhighlight %}
安装的基本的包包含linux内核，一些相应的文件操作工具如ls, cat, tar等，网络工具是dhcpcd，具体可以参考：[Archlinux base](https://www.archlinux.org/packages/?q=base)
6. 生成配置分区文件表
基本系统安装完成后，开始生成分区文件表，如果有其它需要挂载的文件分区，可以在生成文件表前，先通过`mount`来挂载，或者生成fstab文件后，再`mount`后重新执行下面命令重新生成分区表；
{% highlight css %}
genfstab -p /mnt >> /mnt/etc/fstab
{% endhighlight %}
此步的目的是让操作系统自动管理mount；
 
## 切换到新安装系统中进行一些配置工作
7. 切换系统
{% highlight css %}
arch-chroot /mnt
{% endhighlight %}
这里因为基本操作系统已经安装成功到/mnt目的，包含基本操作系统，利用linux中的chroot切换到新的操作系统中，从而在未重启之前做一些基本配置；
这里多说一句，当操作系统挂了无法启动，或者操作系统出问题，都可以采用这种方式对原生操作系统进行修复处理，即通过外接操作系统，将坏掉的操作系统挂载到外接系统中进行修复操作；
8. 配置主机名及语言
{% highlight css %}
echo computer_name > /etc/hostname
vi /etc/locale.gen 
{% endhighlight %}
取消`en.US-UTF-8 UTF-8`  `zh_CN.UTF-8 UTF-8`前面的注释

将英文做为主语言，配置完成后执行下面命令使配置生效
{% highlight css %}
locale-gen
{% endhighlight %}


9. 配置网络在操作系统启动时自动启动
{% highlight css %}
systemctl enable dhcpcd.service
{% endhighlight %}
10. 配置root密码
{% highlight css %}
passwd
{% endhighlight %}


## 安装系统的启动引导
11. 安装grub
{% highlight css %}
pacman -S grub
{% endhighlight %}
这里需要注意一下，如果是在已经有的操作系统上安装新的操作系统，即已经有了grub，则可以不用再重新安装grub，直接安装下面两个命令即可；
12. 安装引导文件：
{% highlight css %}
grub-install --target=i386-pc --recheck /dev/sda
{% endhighlight %}
13. 通过grub-mkconfig生成grub.cfg配置文件
{% highlight css %}
grub-mkconfig -o /boot/grub/grub.cfg
{% endhighlight %}

14. 为保证操作系统在未来滚动更新时出现问题，因此附加安装一个3.14版本的内核，而此内核是稳定版本不会因为滚动更新而破坏，包含下面两个步骤：
{% highlight css %}
pacman -S linux-lts os-prober
{% endhighlight %}
os-prober是用于自动获取多个版本的linux而生成启动界面
备份grub.cfg文件 
{% highlight css %}
cp /boot/grub/grub.cfg /boot/grub/grub.cfg.originalbak
grub-mkconfig -o /boot/grub/grub.cfg
{% endhighlight %}
重启操作系统
{% highlight css %}
umount /mnt & reboot
{% endhighlight %}

 



