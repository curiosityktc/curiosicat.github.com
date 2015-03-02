---
layout: post
title: Archlinux安装小记
category: linux
---

在[Archlinux][1]官网下载最新镜像

windows下用[USBWriter][2]写入U盘

开机从U盘启动，进入安装界面，用ping命令检查网络是否连接，如无网络，则有线连接执行：

	dhcpcd
无线网络执行

	wifi-menu
确保网络连通后，执行

	fdisk -l
查看硬盘，当前可用硬盘`/dev/sda`，建立分区，建议设置交换分区（某些应用可能需要用到），这里笔者建立了俩个分区，一个用作`swap`，一个作为`“/”`

	cfdisk /dev/sda
刷新分区表

	partprobe /dev/sda
格式化分区

	mkswap /dev/sda1
	swapon /dev/sda1
	mkfs.ext4 /dev/sda2
挂载分区到`“/mnt“`

	mount /dev/sda2 /mnt
更换源：打开mirrorlist

	nano /etc/pacman.d/mirrorlist
在mirrorlist中加入国内源：

	Server=http://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
安装基本系统

	pacstrap -i /mnt base base-devel
以UUID的方式生成fstab

	genfstab -U -p /mnt >> /mnt/etc/fstab
改变操作根

	arch-chroot /mnt /bin/bash
设置字符编码，对于中国人去除 locale.gen 中 en_US UTF-8和 zh_CN UTF-8 之前的 #

	nano /etc/locale.gen 
	locale-gen
设置系统默认语言，不要使用中文，会导致tty中文乱码

	echo LANG=en_US.UTF-8 > /etc/locale.conf
设置时区

	ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
	hwclock --systohc --localtime （安装Windows双系统者执行此代码）
	其他执行：hwclock --systohc --utc
设置主机名

	echo 主机名 > /etc/hostname 
	nano  /etc/hosts  在第一个localhost后面加上主机名
压缩内核

	mkinitcpio -p linux
设置 root 密码

	passwd
安装 grub ，双系统还要安装os-prober（单系统执行前两条命令即可）

	pacman -S grub
	grub-install /dev/sda
	pacman -S os-prober
	os-prober
	grub-mkconfig -o /boot/grub/grub.cfg
没有网线的记得安装无线驱动，不然重启无法联网

	pacman -S iw wpa_supplicant
	pacman -S dialog 
安装基本系统完成，重启电脑

	exit
	umount -R /mnt


[1]: https://www.archlinux.org/download/
[2]:http://sourceforge.net/projects/usbwriter/

{% include references.md %}