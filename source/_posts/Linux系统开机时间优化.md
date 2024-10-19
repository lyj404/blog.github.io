---
title: Linux系统开机时间优化
date: 2024-10-19 15:43:23
tags: Linux
categories: Linux
keywords: Linux通用配置文件
cover: https://s21.ax1x.com/2024/09/20/pAKX2o4.png
description: 通过禁用不必要的服务、优化 GRUB 设置和分析启动过程来优化Linux系统开机时间
---
# 优化系统开机时间
## 关闭不必要的开机自启服务

通过`systemctl`命令查看当前启动的服务：

```
systemctl list-unit-files --state=enabled
# 我电脑上启动的服务
UNIT FILE                          STATE   PRESET  
avahi-daemon.service               enabled disabled
bluetooth.service                  enabled disabled
firewalld.service                  enabled disabled
getty@.service                     enabled enabled 
NetworkManager-dispatcher.service  enabled disabled
NetworkManager-wait-online.service enabled disabled
NetworkManager.service             enabled disabled
nvidia-hibernate.service           enabled disabled
nvidia-persistenced.service        enabled disabled
nvidia-resume.service              enabled disabled
nvidia-suspend.service             enabled disabled
optimus-manager.service            enabled disabled
power-profiles-daemon.service      enabled disabled
sddm.service                       enabled disabled
sshd.service                       enabled disabled
systemd-timesyncd.service          enabled enabled 
avahi-daemon.socket                enabled disabled
systemd-userdbd.socket             enabled enabled 
fstrim.timer                       enabled disabled

19 unit files listed.
```

**可以禁用的服务：**

```
# 用于局域网内的设备发现（如Bonjour协议），通常是用于网络发现功能
sudo systemctl disable avahi-daemon.service
# 用户等待网络完全启动后再继续其他服务，对于大多数桌面用户而言可以禁用，因为会导致延长启动时间
sudo systemctl disable NetworkManager-wait-online.service
# 用户NVIDIA驱动管理系统休眠和恢复的，如不需要系统休眠或挂起的功能可以禁用
sudo systemctl disable nvidia-hibernate.service nvidia-suspend.service nvidia-resume.service
# 如不需要集显和独显切换功能，或只有一个显卡的情况可以禁用
sudo systemctl disable optimus-manager.service
# 用于管理电源性能配置，对于电源性能模式管理没啥要求的可以禁用
sudo systemctl disable power-profiles-daemon.service
```

禁用的服务`avahi-daemon.service` `NetworkManager-wait-online.service` `optimus-manager.service`

## 优化GRUB设置

编辑`/etc/default/grub`文件，将`GRUB_TIMEOUT`值减少，例如：

```
# 我的默认值为5，减少这个值
GRUB_TIMEOUT='3'
```

保存`grub`配置后，还需运行以下命令更新GRUB：

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

`GRUB_TIMEOUT=1` 是在 GRUB 配置文件（通常是 `/etc/default/grub`）中用于设置 GRUB 引导菜单显示时间的参数。具体作用如下：

- **显示时间**：设置 GRUB 启动菜单在屏幕上显示的秒数。在这个例子中，`GRUB_TIMEOUT=1` 表示 GRUB 菜单只会显示 1 秒钟，然后自动选择默认的启动项进入系统。
- **自动启动**：如果在 1 秒内没有用户手动选择菜单项，GRUB 会自动启动预设的默认操作系统。

## 分析启动过程

通过`systemd-analyze blame` 和 `systemd-analyze critical-chain` 查看启动过程中哪些服务花费时间最长。

```
systemd-analyze blame
systemd-analyze critical-chain
```

- **blame** 命令会列出启动时间最长的服务，可以帮助你找到启动的瓶颈。
- **critical-chain** 会显示启动关键路径上哪些服务影响了整个启动时间。

**这是我启动时间最长的几个服务，大部分以关闭开机自启动**

```
3.436s plocate-updatedb.service
1.699s optimus-manager.service
1.672s nvidia-persistenced.service
1.139s NetworkManager.service
```

我使用的操作系统是`Endeavour OS`，但对于其他的Linux操作系统也可按照该步骤操作
