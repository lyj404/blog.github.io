---
title: chroot修复Debian
date: 2024-03-27 10:52:44
tags: Linux
categories: Linux
keywords: chroot修复Debian
cover: https://s21.ax1x.com/2024/03/14/pFcL7lj.png
description: chroot是一个Unix-like系统中的命令，用于改变进程的根目录，可用于系统恢复和修复
---
# 问题描述
由于我将Debian系统安装在移动硬盘上，在系统刚装完重启的时候没有出现问题，但在我关机之后，隔离一段时间再次启动移动硬盘中的Debian系统的时候，在BIOS的启动项中就只有电脑本身的EFI启动项了，移动硬盘中的Debian对应的EFI启动项消失了
# 修复准备
需要准备一个Live CD启动盘，可使用`Ventoy`或者是`balenaEtcher`，制作启动盘，并将对应的[Debian Live 12.5 cinnamon](https://iso.mirrors.ustc.edu.cn/debian-cd/current-live/amd64/iso-hybrid/debian-live-12.5.0-amd64-cinnamon.iso)系统的Live CD镜像刻录到启动盘。我使用的cinnamon桌面环境，可选择不同桌面的Live CD。

> Live CD 指的是一种可以在计算机启动时直接从光盘或USB设备中运行的操作系统。用户无需安装操作系统，只需将 Live CD 插入计算机并重启，即可在不影响硬盘上现有操作系统的情况下运行一个完整的操作系统。这种方式通常用于系统故障恢复、操作系统安全性检测、系统测试和演示等目的。
# 开始chroot修复
在电脑开始的时候通过按快捷键的方式进入BIOS启动页面，选择Live CD对应的启动项，进入对应的Debian Live CD中。
## 查看挂载情况
可查用不同的方式查看挂载情况，使用命令行的方式可以采用`lsblk`命令，也可通过直接查看需要修复EFI分区的系统对应的`fstab`文件
### 1.通过lsblk查看挂载情况
```bash
$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 232.9G  0 disk
├─sda1   8:6    0   646M  0 part /boot/efi
├─sda2   8:7    0   78.1  0 part /
└─sda3   8:8    0   948M  0 part /swap
```
### 2.通过`fstab`文件查看挂载情况
`fstab` 文件是 Linux 和类 Unix 系统中的一个重要配置文件，用于指定在系统引导时要挂载的文件系统及其相关参数。
在 `fstab` 文件中，每一行描述了一个文件系统的挂载信息，通常包括以下字段：
1. **设备文件（Device）**：指定要挂载的设备的路径或 UUID（唯一标识符）。
2. **挂载点（Mount Point）**：指定设备挂载到文件系统的哪个目录下。
3. **文件系统类型（Filesystem Type）**：指定设备上的文件系统类型，如 ext4、NTFS、FAT32 等。
4. **挂载选项（Mount Options）**：指定挂载设备时使用的选项，如读写权限、自动挂载等。
5. **备份级别（Dump）**：指定是否要备份该文件系统，一般设置为 0。
6. **检查顺序（fsck order）**：指定系统引导时检查文件系统的顺序，一般设置为 0 表示不检查。
具体操作流程：直接通过桌面的文件管理器进入需要修复的EFI的系统的目录下面，进入`/etc`目录，找到`fatab`文件并打开，该文件内描述了对应引导时要挂载的文件系统。
## 对根分区进行挂载
```bash
sudo mkdir /mnt/debian
sudo mount /dev/sda2 /mnt/debian # sda1为 / 分区所在设备
```
## 对EFI分区进行挂载
在 UEFI 模式下安装 Linux 发行版时，通常会单独为 `/boot/efi` 目录创建一个 EFI 分区。这个 EFI 分区通常是一个 FAT32 格式的分区，用于存储引导加载程序和其他引导相关文件。默认挂载根目录时并不会挂载这个目录，因为它们不在同一个分区，所以需要efi所在的设备进行相应的挂载，否则内核无法重新安装：
```bash
sudo monut /dev/sda1 /mnt/debian/boor/efi
```
除此之外为了确保在chroot环境中能够访问到必要的设备节点和系统信息，需要对一些虚拟目录进行手动绑定，这是因为在chroot环境中，根目录已经被改变，原本的`/dev`、`/sys`、`/proc`等目录已经不再对应实际的设备节点和系统信息。
```bash
sudo mount --bind /dev /mnt/debian/dev
sudo mount --bind /proc /mnt/debian/proc
sudo mount --bind /sys /mnt/debian/sys
```
> 挂载完成之后可以使用`lsblk`命令查看对应的挂载情况，如果对应的分区都对应到/mnt/debian这相关的路径则挂载成功
## 使用chroot对efi进行修复
chroot是一个Unix-like系统中的命令，用于改变进程的根目录。具体来说，chroot命令会将指定目录作为根目录，以至于进程在这个环境下运行时，该目录将被视为根目录，而进程无法访问其他目录。
chroot的主要作用包括：
1. **安全隔离**：通过将进程的根目录更改为指定目录，可以实现进程的安全隔离，使其无法访问系统中的其他文件和目录，从而增加系统的安全性。
2. **测试和开发环境**：在软件开发过程中，可以使用chroot来创建一个独立的环境，用于测试和调试软件，而不影响到系统中其他部分的稳定性。
3. **系统恢复和修复**：在系统遇到问题时，可以使用chroot来进入一个临时的环境，在其中修复系统文件或执行其他操作，以便恢复系统功能。
4. **虚拟化**：在虚拟化环境中，chroot也有一定的作用，可以帮助实现简单的容器化，尽管相比较现代的容器技术，chroot的隔离性和功能更为有限。
```bash
sudo chroot /mnt/debian /bin/bash
```
**执行该命令如果报错 bash: chroot: command not found，可能是用户没有对应的环境变量**
```bash
# 打印用户对应的环境变量
 echo $PATH
# 我的输出结果如下
# /usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
```
**添加对应的环境变量**
```bash
sudo nano /etc/profile
# 在/etc/profile中添加下列环境变量
export PATH=$PATH:/usr/sbin:/sbin
# 使配置生效
source /etc/profile
```
执行chroot命令之后将会切换到root用户，这说明执行成功
# 安装GRUB 
GRUB（GNU Grand Unified Bootloader）是一个开源的引导加载程序，用于在计算机启动时加载操作系统。它允许用户在多个安装的操作系统之间进行选择，并提供了引导配置和管理的功能。GRUB支持多种操作系统，包括各种Linux发行版、Windows、macOS等。

GRUB的主要作用是在计算机启动时负责引导加载操作系统内核，并提供一个菜单界面供用户选择不同的操作系统或启动选项。通过配置GRUB，用户可以自定义引导菹程，添加新的操作系统选项，修改引导参数等。因此，GRUB在启动管理方面扮演着重要的角色，使得用户可以轻松地管理多个操作系统或引导选项。
**执行该命令安装GRUB：**
```bash
grub-install --target=x86_64-efi --efi-directory=/boot/efi/ --removable
# 命令执行之后输入installed finished说明执行成功
```
## 卸载挂载的虚拟目录
```bash
# 从root用户切换成普通用户
exit
# 卸载虚拟目录
sudo umount -Rlv /mnt/debian/{dev,proc,sys}
```
`-R`: 递归地卸载指定目录及其子目录中挂载的文件系统
`-l`:  立即卸载，即使文件系统正被使用也会立刻卸载
`-v`: 显示详细信息，包括每个步骤的操作信息
执行上诉步骤之后，EFI分区就已经修复好了，在重启电脑之后即可在BIOS中看到对应EFI启动项

# cinnamon桌面无法启动问题描述
由于最近删除了一些软件，因此使用了`apt`的`autoremove`命令来清理一些系统中不必要包和依赖，由于`autoremove`命令统计出来可以清理的包名太多，一时没细看，因此导致我的cinnamon桌面挂了，报错信息如下：Filaed to load session 'cinnamon'
## 修复过程
由于我的`network-manager`也被`autoremove`清理了，因此只好通过`chroot`去修复，先按照上诉方法挂在自己的系统
**复制livecd的文件到自己的系统**
```shell
# 目录根据自己的挂在目录来
sudo cp -v /etc/resolv.conf /mnt/debian/etc
```
> 因为我使用的DHCP来自动获取网络配置，而我的网络相关的软件由于已经被卸载，所以需要livecd的网络配置，而DHCP 客户端通常会自动生成或修改 `/etc/resolv.conf` 文件

复制完成之后可以执行上面的chroot命令，执行之后，由于我的network-manager已经别卸载所以需要先安装
```shell
sudo apt install network-manager
```
### 修复cinnamon桌面环境
勤快的可以查看`apt`的日志，从而获取到autoremove移除了那些依赖，`apt`日志目录如下/var/log/apt/`，我由于比较懒惰，因此直接安装cinnamon桌面了
```shell
 sudo apt install task-cinnamon-desktop
```
安装成功之后退出root用户，并按照上诉的教程卸载挂在的虚拟目录。
