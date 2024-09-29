---
title: Endeavour OS安装显卡驱动
date: 2024-09-15 20:32:11
tags: Linux
categories: Linux
keywords: Linux通用配置文件
cover: https://s21.ax1x.com/2024/09/20/pAKX2o4.png
description: Endeavour OS安装显卡驱动记录，arch系列皆通用
---
# 核显驱动安装
## Intel核显驱动
通过以下命令安装Intel的核显驱动包：
```bash
sudo pacman -S mesa lib32-mesa vulkan-intel lib32-vulkan-intel
```
## AMD核显驱动
对于AMD的核显首先需要确定对应的核显架构
**架构对照如下：**

| GPU 架构        | Radeon 显卡         | 开源驱动         | 非开源驱动       |
| ------------- | ----------------- | ------------ | ----------- |
| GCN 4 及之后     | 多种*               | AMDGPU*      | AMDGPU PRO* |
| GCN 3         | 多种                | AMDGPU       | AMDGPU PRO  |
| GCN 2         | 多种                | AMDGPU/ ATI* | 不支持         |
| GCN 1         | 多种                | AMDGPU / ATI | 不支持         |
| TeraScale 2&3 | HD 5000 - HD 6000 | ATI          | 不支持         |
| TeraScale 1   | HD 2000 - HD 4000 | ATI          | 不支持         |
| 旧型号           | X1000 及之前         | ATI          | 不支持         |
AMDGPU驱动安装命令：
```bash
sudo pacman -S mesa lib32-mesa xf86-video-amdgpu vulkan-radeon lib32-vulkan-radeon
```
ATI驱动安装命令：
```bash
sudo pacman -S mesa lib32-mesa xf86-video-ati
```
# NVIDIA独显
独显安装命令：
```bash
sudo pacman -S nvidia-dkms nvidia-settings lib32-nvidia-utils
```
# 双显卡
## 安装GPU切换程序
对于核显和独显并存笔记本电脑，如果想要实现快捷的切换核显和独显，则需安装一个用来管理GPU切换程序`optimus-manager`
```bash
yay -S optimus-manager optimus-manager-qt
```
**将软件设置为开机自启动**
```bash
sudo systemctl enable optimus-manager.service
```
## 安装电源管理
电源管理主要用来确保在使用核显的时候，正确的关闭独显。
```bash
sudo pacman -S bbswitch
```

> 开启独显模式可能会不生效，需要将NVIDIA 持久化守护进程设置为开机自启动，并重启设备，如果还是不生效，则需查看是否是X11协议

```bash
sudo systemctl enable nvidia-persistenced
```
# WayLand下使用NVIDIA显卡驱动
## 配置环境变量
需要在`/etc/environment`下配置相关的环境变量
```shell
GBM_BACKEND=nvidia-drm
__GLX_VENDOR_LIBRARY_NAME=nvidia
ENABLE_VKBASALT=1
LIBVA_DRIVER_NAME=nvidia
```
## kernel modules 配置
需要在`/etc/mkinitcpio.conf`添加相关的配置，如找不到对应的文件，则是系统没有安装`mkinitcpio`，需要先安装`mkinitcpio`，安装之后会自动生成`/etc/mkinitcpio.conf`
```bash
sudo pacman -S mkinitcpio
```
在`/etc/mkinitcpio.conf`中找到`MODULES=()`那一行，并在小括号中添加`nvidia nvidia_modeset nvidia_uvm nvidia_drm`，除此之外还需查看`hooks=()`中是否有`kms`这个值，如有则需删除该值
### 创建配置文件
新建一个文件`/etc/modprobe.d/nvidia.conf`，并向该文件添加一下内容：
```shell
blacklist nouveau
options nvidia_drm modeset=1 fbdev=1
```
最后执行`sudo mkinitcpio -P`，来重新生成initramfs
>以上操作需重启系统，才会生效
