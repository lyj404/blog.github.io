---
title: Linux目录作用详解
date: 2024-03-14 10:30:32
tags: Linux
categories: Linux
keywords: Linux目录作用详解
cover: https://s21.ax1x.com/2024/03/14/pFcL7lj.png
description: 在Linux系统中，不同的目录具有不同的作用和功能，每个目录都扮演着特定的角色，有助于组织和管理文件系统。
---
在Linux系统中，不同的目录具有不同的作用和功能，每个目录都扮演着特定的角色，有助于组织和管理文件系统。
**Linux下各目录的作用：**
| 目录名 | 说明 | 具体示例 |
| ---- | ---- | ---- |
| /bin | 存放系统可执行的二进制文件(命令) | 如：ls、cp、mv等基本命令 |
| /boot | 存放启动Linux系统所需的文件，包括内核文件和引导加载程序(bootloader) | 如：vmliuz（内核文件）、grub（GRUB引导程序） |
| /dev | 存放设备文件，用于与硬件设备进行交互 | 如：sda（硬盘）、tty（终端设备） |
| /etc | 存放系统的配置文件 | 如：passwd（用户密码文件）、hosts（主机名与IP地址映射） |
| /home | 存放普通用户的主目录 | 如：/home/username |
| /lib | 存放共享库文件，为运行时链接额的程序提供支持 | 如：libc.so（c语言库）、libm.so（数字库） |
| /media | 自动挂载可移动介质(如CD、DVD、USB驱动器)的目录 | 如：/media/cdrom、/media/usb |
| /mnt | 手动挂载临时文件系统的目录 | 如：/mnt/cdrom、/mnt/usb |
| /opt | 存放可选的第三方软件和程序包 | 如：/opt/program-name |
| /proc | 虚拟文件系统，提供有关内核和进程的信息 | 如：/proc/cpuinfo（CPU信息）、/proc/meninfo（内存信息） |
| /root | 超级用户(root)的主目录 | /root |
| /sbin | 存放系统管理员使用的系统管理命令 | 如：ifconfig（网络配置）、fdisk（磁盘分区） |
| /tmp | 存放临时文件，系统重启后会清空 | 如：临时下载文件、临时缓存文件 |
| /usr | 存放用户和系统共享的只读数据，类似于Program Files目录 | 如：/usr/bin（用户可执行的二进制文件）/usr/lib（共享库文件） |
| /var | 存放经常变化的文件，比如日志、缓存和临时文件 | 如：/var/log（日志文件）、/var/cache（缓存文件） |
| /srv | 存放服务（services）相关的数据文件，比如说HTTP服务器、FTP服务器 | 如：/srv/www（web服务器文件）、/srv/ftp（FTP服务器文件） |
| /usr/local | 存放用户自行安装的软件和程序 | 如：/usr/local/bin、/usr/local/lib |

GRUB（GNU GRand Unified Bootloader）是一个广泛使用的开源引导加载程序，用于在计算机启动时加载操作系统。它具有以下主功能和特点：
1. **多操作系统支持**：GRUB支持多种操作系统和内核的启动，包括Linux、Windows、Mac OS等，使用户能够在同一计算机上选择不同的操作系统进行启动。
2. **灵活性**：GRUB提供了丰富的配置选项和命令行界面，可以根据需要进行灵活的配置和管理，如添加、删除或修改启动菜单项等。
3. **模块化设计**：GRUB采用模块化的设计，允许将引导加载程序和文件系统驱动程序作为独立的模块加载，从而提高了系统的灵活性和可维护性。
4. **图形界面支持**：GRUB2版本引入了对图形界面的支持，使用户可以通过图形菜单界面轻松选择启动项，提升了用户体验。
5. **错误恢复**：GRUB具有强大的错误恢复功能，可以在系统引导出现问题时提供修复选项，帮助用户解决引导问题。
6. **配置文件**：GRUB使用grub.cfg文件（或者在旧版本中使用menu.lst文件）来存储启动菜单的配置信息，用户可以编辑该文件来自定义引导菜单。

