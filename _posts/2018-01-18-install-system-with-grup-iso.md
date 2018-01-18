---
layout: articles_item
title: 使用grub（非grub2）+ISO镜像文件安装RHEL7.3
categories: [linux bootloader grub]
---

## 硬件需求：
  * 硬盘1： 已安装RHEL6.8, 有两个分区, 第一个分区的挂载点是/boot,
             第二个分区的类型是LVM
  * 硬盘2： 全新，没有操作系统

## 提取文件
从RHEL7.3的ISO镜像中提取下面的文件
```
/isolinux/vmlinuz
/isolinux/vmlinuz,initrd.img
/LiveOS/squashfs.img
```

并保存到硬盘1的/boot目录中，文件结构如下：
```
/boot/isolinux/vmlinuz
/boot/isolinux/vmlinuz,initrd.img
/boot/LiveOS/squashfs.img
```

在/boot/grub/grub.conf文件中添加启动项，如下：
```
title Install Red Hat Enterprise Linux 7.3
  root (hd0,0)
  kernel /isolinux/vmlinuz inst.repo=hd:/dev/sda1:/
  initrd  /isolinux/initrd.img
```
## 准备安装
重启系统，并选择从新添加的启动项启动，就进入到
RHEL7.3的安装界面，可以根据自己环境选择安装的方式

由于硬盘1/boot是的独立分区，并且只有500MB左右，不能存放
RHEL7.3的整个ISO文件。其他的分区是LVM，grub不能识别LVM
格式的分区。所以选择从网络安装。在安装的配置界面配置网络。

配置一个文件服务器(ftp,http.nfs), 并将RHEL7.3的ISO文件存放到
服务器的响应位置，并可以通过对应的协议能够访问。

然后，在安装界面中配置与服务对应的安装方式，并安装RHEL7.3。