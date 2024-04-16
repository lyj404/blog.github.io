---
title: Debian实现核显和Nvidia独显切换
date: 2024-04-16 14:56:09
tags: Debian
categories: Linux
keywords: Debian
cover: https://s21.ax1x.com/2024/04/16/pFxKR2t.png
description: Debian并没有像Ubuntu一样有nvidia-prime的软件包，因此不能使用sudo prime-select来切换显卡，但是有一个名叫envycontrol的开源项目可是实现通过使用命令行切换来GPU。
---
# 安装Nvidia 驱动
**更新软件源**
```bash
sudo apt update
```
**安装显卡驱动**
> 通常情况下使用apt安装`nvidia-driver`即可成功安装驱动，如果不确定可安装`nvidia-detect`命令识别GPU 来确认推荐的驱动程序包


```bash
sudo apt install nvidia-driver
# 安装 nvidia-detect
sudo apt install nvidia-detect
```
**识别GPU推荐的驱动**
```bash
nvidia-detect
# 输入信息如下：
Detected NVIDIA GPUs:
01:00.0 VGA compatible controller [0300]: NVIDIA Corporation GP107M [GeForce GTX 1050 3 GB Max-Q] [10de:1c91] (rev a1)

Checking card:  NVIDIA Corporation GP107M [GeForce GTX 1050 3 GB Max-Q] (rev a1)
Your card is supported by all driver versions.
Your card is also supported by the Tesla 470 drivers series.
It is recommended to install the
    nvidia-driver
package.
```
**检查驱动是否成功安装**
> 如果提示错误 NVIDIA-SMI has failed because it could not communicate with the NVIDIA driver. 那么是因为电脑或者笔记本启用了安全启动，解决方法为进 BIOS 关闭安全启动，或者使用 mokutil 更新 Nvidia UEFI 安全启动签名
```bash
nvidia-smi
# 如果报命令找不到，请先安装命令
sudo apt install nvidia-smi
```
# 禁用开源驱动
```bash
sudo echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf
sudo update-initramfs -u
```
> 如果报权限不足，请使用root用户

```bash
su
echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf
update-initramfs -u
#输出信息如下：
update-initramfs: Generating /boot/initrd.img-6.1.0-18-amd64
#退出root用户
exit
#重启电脑
reboot
```
# 在核显和独显之间切换
Debian并没有像Ubuntu一样有`nvidia-prime`的软件包，因此不能使用`sudo prime-select 核显或独显名字`来切换显卡，但是有一个名叫[envycontrol](https://github.com/bayasdev/envycontrol)的开源项目可是实现通过和`nvidia-prime`一样使用命令行切换GPU。
**安装envycontrol**
```bash
# 进入安装包所在的目录使用一下命令安装
sudo apt install ./python3-envycontrol_3.4.0-1_all.deb
```
安装之后即可使用envycontrol来切换GPU模式了
**切换到核显的命令**
```bash
sudo envycontrol -s integrated
```
**切换到混合模式的命令**
```bash
sudo envycontrol -s hybrid --rtd3
```
**切换到 NVIDIA 独立显卡的命令**
```bash
sudo envycontrol -s nvidia --force-comp
```
> 切换完成之后需要重启电脑才能生效

```bash
reboot
```


