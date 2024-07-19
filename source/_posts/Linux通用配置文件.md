---
title: Linux通用配置文件
date: 2024-07-19 22:47:54
tags: Linux
categories: Linux
keywords: Linux通用配置文件
cover: https://s21.ax1x.com/2024/03/14/pFcL7lj.png
description: 在不同的Linux发行版本中都存在这一些通用的配置文件，这是一些通用配置文件的写法
---
# desktop文件
在日常使用Linux系统的时候，经常会遇到一些软件只提供了可执行文件，没有提供可以直接通过包管理工具安装的途径，比如说常见的`AppImage` 文件或者是一些没有对特定发行版本进行安装包编译的开源项目，这些软件就只能通过命令行或者是在文件管理器下找到对应的启动文件在启动比较繁琐，不像那些通过包管理工具安装的软件，可以有桌面图标或者直接应用菜单中显示；而想要这些软件更包管理工具一样拥有桌面图标/应用菜单中显示，可以编写对应的`desktop`文件。

>`desktop`文件是一个包含应用程序信息的存文本文件，通常根据所有用户的可见性的不同可以放在`~/.local/share/applications/`（针对当前用户）或者是`/usr/share/applications/`（针对所有用户）下。

## `.desktop`文件常用字段
* Type：通常为Application（应用程序）或者是Link（URL链接）
* Name：应用程序显示的名称
* Comment：简单的描述，通常在鼠标悬停时显示
* GenericName：为应用程序提供一个通用的名称，用助于用户理解应用程序的功能
* Exec：可执行文件的路径
* Icon：应用的图标
* StartupNotify：启动应用程序是否发送通知，值为true或false
* StartupWMClass：置顶应用程序窗口管理器的类名，有助于窗口管理器识别和分组应用程序的窗口
* MimeType：指定了应用程序支持的 MIME 类型
* [Desktop Action new-empty-window]：表示开始定义一个桌面动作
* Keywords：为应用程序提供关键词，有助于用户在搜索应用程序时找到它
* Terminal：是否在终端中运行，值为true或false
* Categories：应用程序的分类，用逗号分隔

> 在 .desktop 文件中，MimeType 字段用于指定应用程序可以处理的文件类型。
## 具体案例
```text
[Desktop Entry]
Name=Visual Studio Code
Comment=Code Editing. Refined.
GenericName=Text Editor
Exec=/usr/bin/code %F
Icon=visual-studio-code
Type=Application
StartupNotify=false
StartupWMClass=Code
Categories=TextEditor;Development;IDE;
MimeType=text/plain;inode/directory;application/x-code-workspace;
Actions=new-empty-window;
Keywords=vscode;

[Desktop Action new-empty-window]
Name=New Empty Window
Exec=/usr/bin/code --new-window %F
Icon=visual-studio-code
```
**Categories的具体内容可以访问：**
> https://specifications.freedesktop.org/menu-spec/latest/apa.html
# service文件
`.service` 文件是`systemd`系统中的一个单元（Unit）配置文件，用于定义和配置系统服务，使得系统管理员可以更容易地管理这些服务的启动、停止和其他行为。而`Systemd`是一系列工具的集合，其作用不仅可以启动操作系统，还可以管理后台服务、日志归档、设备管理、电源管理、定时任务等等。
**service文件存放路径：**
1. `/etc/systemd/system/`这是默认的系统服务文件路径，通常存放系统级服务
2. `/lib/systemd/system/`通常是用于存放预安装的系统服务文件
3. `/etc/systemd/user/`存放用户级服务
4. `~/.config/systemd/user/`存放单个用户的服务
5. `/run/systemd/system/`：用于存放运行时生成的系统服务文件
6. `/run/systemd/user/`：用于存放运行时生成的用户级服务文件

## 文件基本结构
* `[Unit]`: 包含描述信息、依赖关系
	*  `Description`：简短描述
	* `Documentation`：文档地址
	* `Requires`：当前 Unit 依赖的其他 Unit，如果它们没有运行，当前 Unit 会启动失败
	* `Wants`：与当前 Unit 配合的其他 Unit，如果它们没有运行，当前 Unit 不会启动失败
	* `BindsTo`：与`Requires`类似，它指定的 Unit 如果退出，会导致当前 Unit 停止运行
	* `Before`：如果该字段指定的 Unit 也要启动，那么必须在当前 Unit 之后启动
	* `After`：如果该字段指定的 Unit 也要启动，那么必须在当前 Unit 之前启动
	* `Conflicts`：这里指定的 Unit 不能与当前 Unit 同时运行
	* `Condition...`：当前 Unit 运行必须满足的条件，否则不会运行
	* `Assert...`：当前 Unit 运行必须满足的条件，否则会报启动失败
* `[Service]`: 定义服务的核心配置，如如何启动和停止服务
	* `WantedBy`：它的值是一个或多个 Target，当前 Unit 激活时（enable）符号链接会放入`/etc/systemd/system`目录下面以 Target 名 + `.wants`后缀构成的子目录中
	* `RequiredBy`：它的值是一个或多个 Target，当前 Unit 激活时，符号链接会放入`/etc/systemd/system`目录下面以 Target 名 + `.required`后缀构成的子目录中
	* `Alias`：当前 Unit 可用于启动的别名
	* `Also`：当前 Unit 激活（enable）时，会被同时激活的其他 Unit
* `[Install]`: 定义服务的安装信息，如何启用或禁用
	* `Type`：定义启动时的进程行为。它有以下几种值。
	- `Type=simple`：默认值，执行`ExecStart`指定的命令，启动主进程
	- `Type=forking`：以 fork 方式从父进程创建子进程，创建后父进程会立即退出
	- `Type=oneshot`：一次性进程，Systemd 会等当前服务退出，再继续往下执行
	- `Type=dbus`：当前服务通过D-Bus启动
	- `Type=notify`：当前服务启动完毕，会通知`Systemd`，再继续往下执行
	- `Type=idle`：若有其他任务执行完毕，当前服务才会运行
	- `ExecStart`：启动当前服务的命令
	- `ExecStartPre`：启动当前服务之前执行的命令
	- `ExecStartPost`：启动当前服务之后执行的命令
	- `ExecReload`：重启当前服务时执行的命令
	- `ExecStop`：停止当前服务时执行的命令
	- `ExecStopPost`：停止当其服务之后执行的命令
	- `RestartSec`：自动重启当前服务间隔的秒数
	- `Restart`：定义何种情况 Systemd 会自动重启当前服务，可能的值包括`always`（总是重启）、`on-success`、`on-failure`、`on-abnormal`、`on-abort`、`on-watchdog`
	- `TimeoutSec`：定义 Systemd 停止当前服务之前等待的秒数
	- `Environment`：指定环境变量
## 具体案例
```text
[Unit]
# 服务描述
Description=Bluetooth service
# 文档链接，指向 bluetoothd 的 man 页面
Documentation=man:bluetoothd(8)
# 条件：只有当 /sys/class/bluetooth 目录存在时才启动此服务
ConditionPathIsDirectory=/sys/class/bluetooth

[Service]
# 服务类型：dbus，表示这是一个 D-Bus 服务
Type=dbus
# D-Bus 服务的名称
BusName=org.bluez
# 启动命令：执行 bluetoothd 守护进程
ExecStart=/usr/lib/bluetooth/bluetoothd
# 允许服务通知系统其主进程的就绪状态
NotifyAccess=main
# 看门狗超时设置（当前被注释）
#WatchdogSec=10
# 服务崩溃后的重启策略（当前被注释）
#Restart=on-failure
# 设置进程的能力边界，只允许网络管理和绑定服务端口的能力
CapabilityBoundingSet=CAP_NET_ADMIN CAP_NET_BIND_SERVICE
# 限制进程可以创建的最大线程数为 1
LimitNPROC=1

# 文件系统锁定
# 保护 home 目录，禁止服务访问
ProtectHome=true
# 严格保护系统目录，使其只读
ProtectSystem=strict
# 为服务提供私有的 /tmp 目录
PrivateTmp=true
# 保护内核可调参数，禁止修改
ProtectKernelTunables=true
# 保护控制组，禁止修改
ProtectControlGroups=true
# 指定服务的状态目录
StateDirectory=bluetooth
# 设置状态目录的权限模式
StateDirectoryMode=0700
# 指定服务的配置目录
ConfigurationDirectory=bluetooth
# 设置配置目录的权限模式
ConfigurationDirectoryMode=0555

# 执行映射
# 禁止内存中的写入和执行操作，提高安全性
MemoryDenyWriteExecute=true

# 权限提升
# 禁止获取新的权限，提高安全性
NoNewPrivileges=true

# 实时性
# 限制实时调度，防止服务影响系统实时性能
RestrictRealtime=true

[Install]
# 指定此服务应该在哪个目标（target）下启用
WantedBy=bluetooth.target
# 为此服务创建一个别名，用于 D-Bus 激活
Alias=dbus-org.bluez.service
```
## 管理命令
```shell
# 启动服务
systemctl start app.service
# 停止服务
systemctl stop app.service
# 重启服务
systemctl restart app.service
# 使服务开机自启动
systemctl enable app.service
# 关闭开机自启动
systemctl disable app.service
# 查看服务状态
systemctl status app.service
```
