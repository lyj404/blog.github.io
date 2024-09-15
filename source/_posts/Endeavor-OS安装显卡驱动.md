---
title: Endeavor OS安装显卡驱动
date: 2024-09-15 20:32:11
tags: Linux
categories: Linux
keywords: Linux通用配置文件
cover: https://s21.ax1x.com/2024/03/14/pFcL7lj.png
description: Endeavor OS安装显卡驱动记录，arch系列皆通用
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

> 开启独显模式可能会不生效，需要将NVIDIA 持久化守护进程设置为开机自启动，并重启设备，如果还是不生效，尝试将WayLand切换到Xorg，NVIDIA驱动对于WayLand支持不够友好

```bash
sudo systemctl enable nvidia-persistenced
```
