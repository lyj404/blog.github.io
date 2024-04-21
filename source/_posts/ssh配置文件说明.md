---
title: ssh配置文件说明
date: 2024-04-21 21:45:17
tags: Linux
categories: Linux
keywords: ssh配置文件说明
description: OpenSSH是SSH（Secure Shell）协议的免费且开源的实现，主要用于安全地远程控制计算机或在计算机之间传输文件。SSH协议通过加密通道确保了数据传输的安全性，而OpenSSH则提供了这一协议的具体应用。 为了提升用户体验和操作便捷性，OpenSSH客户端允许用户通过一个名为config的配置文件自定义SSH连接的参数。
---
`OpenSSH`是SSH（Secure Shell）协议的免费且开源的实现，主要用于安全地远程控制计算机或在计算机之间传输文件。SSH协议通过加密通道确保了数据传输的安全性，而OpenSSH则提供了这一协议的具体应用。 为了提升用户体验和操作便捷性，OpenSSH客户端允许用户通过一个名为`config`的配置文件自定义SSH连接的参数。这个配置文件位于用户的`~/.ssh`目录中，该目录通常会在用户首次运行SSH命令时自动创建。通过编辑`config`文件，用户可以为不同的主机设置特定的连接选项，如自定义的用户名、端口号、使用的私钥文件等，从而简化日常的SSH连接操作。

> `~/.ssh/config`该位置的配置通常只影响特定用户，`/etc/ssh/ssh_config`位置的配置是全局配置文件，影响系统上的所有用户

# 基本语法
`config`配置文件由一系列的配置指令实现，每一个指令占一行。注释以`#`开头
# 常用指令
1. **Host：** 用于指定后继指令适用的主机。可以是单个主机名、IP地址，或者使用通配符`*`来匹配所有主机。
2. **HostName：** 指定要连接的远程主机的名称或IP地址。
3. **Port：** 指定远程主机上的端口号，默认为22
4. **User：** 指定要使用的远程用户名。
5. **IdentityFile：** 指定包含用户的私钥的文件路径。
6. **IdentitiesOnly：** 设置为`yes`时，仅使用`IdentityFile`中指定的私钥进行认证。
7. **AddKeysToAgent：** 设置为`yes`时，将私钥添加到`ssh-agent`。
8. **UseKeychain：** 在macOS上，指定是否使用macOS的keychain存储私钥。
10. **ForwardAgent：** 指定一个或多个跳板机（jump host），格式为`user@host`，用于连接到目标主机。
11. **LocalForward：** 设置为`yes`时，允许转发本地的认证代理（如`ssh-agent`）到远程主机。
12. **RemoteForward：** 设置远程端口转发。
> `ssh-agent`用于在用户使用 SSH 进行认证时，自动提供用户的私钥。它是 Openssh 的一部分，
## 示例配置
```text
# 为所有主机设置默认的用户名
Host *
  User lyj

# 连接到example.com的特定配置
Host example.com
  HostName real.example.com
  User specialuser
  Port 20
  IdentityFile ~/.ssh/my_special_key

# 使用跳板机连接到目标主机
Host targethost
  ProxyJump user@jumphost

# 本地端口转发
Host gateway
  LocalForward 8080 localhost:80
```
> 在使用ssh命令的时候，默认先查找全局配置文件，然后在查找用户的配置文件。如果找到匹配的`Host`条目，它会应用相应的配置指令。
