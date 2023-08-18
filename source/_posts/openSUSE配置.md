---
title: openSUSE配置
date: 2023-08-17 21:11:51
tags: openSUSE
categories: Linux
description: openSUSE配置相关的开发环境
---
# 系统配置
## 命令行配置软件源
1. 禁用原有的软件源
```shell
sudo zypper mr -da
```
2. 添加openSuSe Tumbleweed的清华镜像站的软件源
```shell
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/tumbleweed/repo/oss/' mirror-oss
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/tumbleweed/repo/non-oss/' mirror-non-oss
```
3. 刷新软件源
```shell
sudo zypper ref
```

## 常用zypper命令
```shell
# 查看帮助
zypper --help

#更新本地软件包列表，请将 <package> 替换为相应的软件包包名
zypper refresh 
zypper ref #简短版本

#安装新软件包
zypper install <package>
zypper in <package>

#删除一个软件包
zypper remove <package> 
zypper rm <package>

#升级一个软件包
zypper update <package> 
zypper up <package>

#升级全部软件包（Tumbleweed）
zypper dist-upgrade 
zypper dup

#搜索软件包
zypper search <keyword> 
zypper se <keyword> 

#列出全部的软件源
zypper lr -P 

#删除软件的同时清楚软件依赖
sudo zypper rm <package> --clean-deps # 将 <package> 替换为你要删除软件包的包名
sudo zypper rm -u <package> #自动删除要卸载的软件包后不再需要的依赖项。
```

## 更新系统
1. openSUSE Tumbleweed更新系统的命令：
```shell
sudo zypper dup
```
2. 查看哪些服务需要重启
```shell
sudo zypper ps -s
```
3. 重启电脑
```shell
reboot
```
## 关闭自动更新
```shell
sudo systemctl mask packagekit.service #屏蔽 Packagekit 服务
```

## Flatpak
Flatpak 是一款用于 Linux 软件部署和软件包管理的工具。它为软件提供了一个沙箱环境，在这个环境中，用户可以在与系统其他部分隔离的情况下运行应用软件
安装Flatpak ：
```shell
 sudo zypper in flatpak
```
添加 Flatpak 仓库：
```shell
flatpak remote-add --if-not-exists flathub [https://flathub.org/repo/flathub.flatpakrepo](https://flathub.org/repo/flathub.flatpakrepo)
```

### Flatpak基本命令
搜索软件：
```shell
flatpak search [软件名称] #如： flatpak search atom
```
安装软件：
```shell
flatpak install [软件名称] #如： flatpak install Atom
```
运行软件：
```shell
 flatpak run [应用 ID] #如： flatpak run io.atom.Atom
```
查看已安装软件：
```shell
 flatpak list
```
卸载软件：
```shell
flatpak uninstall [软件名称]
```
将Flatpak软件安装到用户目录：
```shell
flatpak --user install flathub io.github.xiaoyifang.goldendict_ng
```
国产软件：
```
WPS： com.wps.Office
QQ： com.qq.QQ
网易云音乐： com.netease.CloudMusic 
QQ Music： com.qq.QQmusic
Icalingua++： io.github.Icalingua.Icalingua
Wemeet： com.tencent.wemeet
```

## AppImage
`AppImage`是一种在 Linux 系统中用于分发便携式软件而不需要超级用户权限来安装它们的格式。
`AppImageLauncher`可以`AppImage`集成到应用程序启动器
### 安装&使用
1. 从 [GitHub](https://github.com/TheAssassin/AppImageLauncher/releases/download/continuous/appimagelauncher-2.2.0-gha111.d9d4c73.x86_64.rpm) 下载 
2. 在终端，移动到 AppImageLauncher 的下载目录，运行 `sudo zypper in appimagelauncher-2.2.0-gha111.d9d4c73.x86_64.rpm` 即可安装 AppImageLauncher。
3. 打开应用程序启动器，打开 `AppImageLauncher Settings`，点击 `appImageLauncherd`，添加你常用于存放 appimage 文件的目录。默认的目录是 `~/Applications`。
4. AppImageLauncher 会自动识别存放在指定目录的 appimage 文件，然后将它们添加到应用程序启动器中。

## 设置命令代理
安装 proxychains-ng :
```shell
sudo zypper in proxychains-ng
```
编辑配置文件：
```shell
sudo nano /etc/proxychains.conf
```
>proxychains.conf文件默认有一个socks4的配置，需要注释，不然会冲突
```shell
http 127.0.0.1 7890
socks5 127.0.0.1 7890
```
使用代理：
```shell
proxychains4 <你的命令行>
```
下载Clash for windows软件：[Clash.for.Windows-0.20.32-x64-linux.tar.gz](https://github.com/Fndroid/clash_for_windows_pkg/releases/download/0.20.32/Clash.for.Windows-0.20.32-x64-linux.tar.gz)

## 安装字体
```shell
sudo zypper search font
```
刷新字体
```shell
fc-cache -fv
```
## Zsh
1. 安装zsh
```shell
sudo zypper in zsh
```
2. 下载oh-my-zsh
```shell
git clone [https://github.com/robbyrussell/oh-my-zsh.git](https://github.com/robbyrussell/oh-my-zsh.git) ~/.oh-my-zsh
```
3. 备份 zsh 配置
```shell
mv .zshrc .zshrc.backup
```
4. 使用`oh-my-zsh`配置
```shell
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```
5. 将`zsh`设为默认shell
```shell
chsh -s /bin/zsh
```
6. 安装主题美化
```shell
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
```
7. 命令提示插件
```shell
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```
	配置插件
```bash
plugins=(
    # other plugins...
    zsh-autosuggestions  # 插件之间使用空格隔开
)
```
8. 语法高亮插件
```shell
#安装插件
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting 
```
```bash
#启用插件
plugins=(
    # other plugins...
    zsh-autosuggestions
    zsh-syntax-highlighting
)
```
9. 开启解压插件
```bash
plugins=(
     # other plugins...
     zsh-autosuggestions
     zsh-syntax-highlighting
     z
)
```
>zsh报`not found theme powerlevel10k`之类的信息

打开`~/.zshrc`文件，注释`ZSH_THEME="powerlevel10k/powerlevel10k"`

## Nvidia显卡驱动
导入Nvidia GPG密钥：
```shell
sudo zypper in openSUSE-repos-NVIDIA
```
添加Nvidia软件源：
```shell
sudo zypper addrepo --refresh [https://download.nvidia.com/opensuse/tumbleweed](https://download.nvidia.com/opensuse/tumbleweed) NVIDIA
```
获取架构信息：
```shell
lspci | grep VGA
```
驱动版本：
* GeForce 700 系列（Kepler）及更高版本，即 NVIDIA 于 2013 年 4 月后发售的显卡，安装`nvidia-video-G06`
*  GeForce 600 系列，安装`x11-video-nvidiaG05`
* GTX4xx/5xx Fermi 系列，安装`x11-video-nvidiaG04`
安装显卡驱动：
```shell
zypper install nvidia-video-G06
```
重启电脑`reboot`
使用`nvidia-smi`命令：
```shell
zypper install nvidia-video-G06-compute-utils
```
>对于同时用于核显和独享的电脑，显卡驱动安装之后重启电脑可能会黑屏，使用`Ctrl + Alt +F3`进入命令行界面

切换显卡：
```shell
# 切换独享
sudo prime-select nvidia
# 切换核显
sudo prime-select intel
```
**注销电脑**

# 系统急救
按`Ctrl + Alt + F1`进入内核终端页面
按`Ctrl + Alt + F3`进入命令行界面
按`Ctrl + Alt + F7`切换会桌面环境

# 开发配置
## Docker
安装docker和docker-compose
```shell
zypper install docker python3-docker-compose
```
设置开机自启动
```shell
sudo systemctl enable docker
```
## VSCode
```shell
#导入密钥
sudo rpm --import [https://packages.microsoft.com/keys/microsoft.asc](https://packages.microsoft.com/keys/microsoft.asc)
#添加软件源
sudo zypper addrepo [https://packages.microsoft.com/yumrepos/vscode](https://packages.microsoft.com/yumrepos/vscode) vscode
#刷新软件源
sudo zypper refresh
#安装vscode
sudo zypper install code
```