---
layout: articles_item
title: 从ISO镜像文件安装RHEL7.3
categories: [linux bootloader grub]
---

本篇说明了从已经存在的RHEL6的系统，使用RHEL7.3的ISO文件安装RHEL7.3操作系统，
到另一个硬盘中。不需要将ISO刻录成光盘。

## 硬件需求：
  * 硬盘1： 已安装RHEL6.8, 有两个分区, 第一个分区的挂载点是/boot,
          第二个分区的类型是LVM
  * 硬盘2： 全新，没有操作系统

## 提取文件
从RHEL7.3的ISO镜像中提取下面的文件
```
/images/pxeboot/vmlinuz
/images/pxeboot/initrd.img
```

并保存到硬盘1的/boot目录中，文件结构如下：
```
/boot/images/pxeboot/vmlinuz
/boot/images/pxeboot/initrd.img
```

在/boot/grub/grub.conf文件中添加启动项，如下：
```
title Install Red Hat Enterprise Linux 7.3
  root (hd0,0)
  kernel /images/pxeboot/vmlinuz inst.repo=ftp://192.168.141.254/7.3 ip=dhcp
  initrd /images/pxeboot/initrd.img
```

如果/boot目录不是独立的分区，则使用下面的配置:
```
title Install Red Hat Enterprise Linux 7.3
  root (hd0,0)
  kernel /boot/images/pxeboot/vmlinuz inst.repo=ftp://192.168.141.254/7.3 ip=dhcp
  initrd /boot/images/pxeboot/initrd.img
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

## 参考
1. https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide/chap-anaconda-boot-options