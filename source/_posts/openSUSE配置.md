---
title: openSUSE配置
date: 2023-08-17 21:11:51
tags: OpenSUSE
categories: Linux
keywords: openSUSE配置
cover: https://z1.ax1x.com/2023/09/23/pPT3AVU.png
description: openSUSE配置相关的开发环境
---
# 基本配置
## 命令行配置软件源
1. 禁用原有的软件源
```shell
sudo zypper mr -da
```
2. openSuSe Tumbleweed更换清华软件源
```shell
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/tumbleweed/repo/oss/' mirror-oss
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/tumbleweed/repo/non-oss/' mirror-non-oss
```
openSUSE Leap15.2或更高版本更换清华软件源
```shell
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/distribution/leap/$releasever/repo/oss/' mirror-oss
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/distribution/leap/$releasever/repo/non-oss/' mirror-non-oss
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/update/leap/$releasever/oss/' mirror-update
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/update/leap/$releasever/non-oss/' mirror-update-non-oss
```
Leap 15.3 用户还需添加 sle 和 backports 源
```shell
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/update/leap/$releasever/sle/' mirror-sle-update
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/update/leap/$releasever/backports/' mirror-backports-update
```
> Leap 15.3 注：若在安装时没有启用在线软件源，sle 源和 backports 源将在系统首次更新后引入，请确保系统在更新后仅启用了六个所需软件源。可使用 zypper lr 检查软件源状态，并使用 zypper mr -d 禁用多余的软件源
3. 添加中国软件源
```shell
sudo zypper addrepo 'https://download.opensuse.org/repositories/home:/opensuse_zh/openSUSE_Tumbleweed' openSUSE_zh
```
> 中国软件源包含WPS、网易云音乐等
4. 刷新软件源
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
# 卸载程序，注意添加 --clean-deps 或者 -u，否则不会卸载依赖项！
zypper remove --clean-deps <package-name>

#升级一个软件包
zypper update <package>
zypper up <package>

#升级全部软件包（Tumbleweed）
zypper dist-upgrade
zypper dup

#搜索软件包
zypper search <keyword>
zypper se <keyword>
# 查找本地安装的程序
zypper search --installed-only  <package-name>

#列出全部的软件源
zypper lr -P

#删除软件的同时清楚软件依赖
sudo zypper rm <package> --clean-deps # 将 <package> 替换为你要删除软件包的包名
sudo zypper rm -u <package> #自动删除要卸载的软件包后不再需要的依赖项。

#添加rpm源
rpm -ivh <packname>.rpm
#删除rpm源
rpm -e <packname>.rpm
```

## 更新系统
1. openSUSE Tumbleweed更新系统的命令：
```shell
sudo zypper dup
```
```shell
# opensuse leap更新系统的命令
sudo zypper update
```
2. 查看哪些服务需要重启
```shell
sudo zypper ps -s
```
3. 重启电脑
```shell
reboot
```


## Flatpak
Flatpak 是一款用于 Linux 软件部署和软件包管理的工具。它为软件提供了一个沙箱环境，在这个环境中，用户可以在与系统其他部分隔离的情况下运行应用软件
**安装Flatpak**
```shell
 sudo zypper in flatpak
```
**添加 Flatpak 仓库**
```shell
flatpak remote-add --if-not-exists flathub [https://flathub.org/repo/flathub.flatpakrepo](https://flathub.org/repo/flathub.flatpakrepo)
```

### Flatpak基本命令
**搜索软件**
```shell
flatpak search [软件名称] #如： flatpak search atom
```
**安装软件**
```shell
flatpak install [软件名称] #如： flatpak install Atom
```
**运行软件**
```shell
 flatpak run [应用 ID] #如： flatpak run io.atom.Atom
```
**查看已安装软件**
```shell
 flatpak list
```
**卸载软件**
```shell
flatpak uninstall [软件名称]
```
**将Flatpak软件安装到用户目录**
```shell
flatpak --user install flathub io.github.xiaoyifang.goldendict_ng
```
**国产软件**
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
**安装&&使用**
1. 从 [GitHub](https://github.com/TheAssassin/AppImageLauncher/releases/download/continuous/appimagelauncher-2.2.0-gha111.d9d4c73.x86_64.rpm) 下载
2. 在终端，移动到 AppImageLauncher 的下载目录，运行 `sudo zypper in appimagelauncher-2.2.0-gha111.d9d4c73.x86_64.rpm` 即可安装 AppImageLauncher。
3. 打开应用程序启动器，打开 `AppImageLauncher Settings`，点击 `appImageLauncherd`，添加你常用于存放 `AppImage`文件的目录。默认的目录是 `~/Applications`。
4. **AppImageLauncher** 会自动识别存放在指定目录的`AppImage`文件，然后将它们添加到应用程序启动器中。

## 使终端走代理
**安装 proxychains-ng**
```shell
sudo zypper in proxychains-ng
```
**编辑配置文件**
```shell
sudo nano /etc/proxychains.conf
```
>proxychains.conf文件默认有一个socks4的配置，需要注释，不然会冲突
```shell
# 将port更换成代理软件的端口
http 127.0.0.1 port
socks5 127.0.0.1 port
```
**使用代理**
```shell
proxychains4 <你的命令行>
```
下载clash-nyanpasu[clash-nyanpasu](https://github.com/keiko233/clash-nyanpasu/releases/download/v1.4.0/clash-nyanpasu_1.4.0_amd64.AppImage)

## 安装字体
```shell
# 使用命令安装
sudo zypper search font
```
**手动安装，直接下载Nerd Font字体**
[FiraCode Nerd Font](https://github.com/ryanoasis/nerd-fonts/releases/download/v3.1.1/FiraCode.zip)
对于下载之后的字体解压，并复制到`/usr/share/fonts/`目录下
> `/usr/share/fonts/`是系统字体目录，`~/.local/share/fonts/`是用户字体目录，`/usr/share/X11/fonts/`是x11字体目录
**刷新字体**
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
git clone https://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
```
3. 使用`oh-my-zsh`配置
```shell
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```
4. 将`zsh`设为默认shell
```shell
chsh -s /bin/zsh
```
5. 配置`~/.zshrc`文件，使得全局命令生效
```shell
export PATH=$HOME/bin:/usr/local/bin:/sbin:/usr/sbin:$PATH
```
6. 安装主题美化
```shell
git clone https://hub.fastgit.org/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k
```
> 设置主题是提示`readonly`，使用以下命令`sudo chown lyj:lyj ~/.zshrc`
7. 命令提示插件
```shell
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```
8. 语法高亮插件
```shell
#安装插件
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```
9. 修改配置文件`~/.zshrc`
```bash
plugins=(
     git
     # other plugins...
     zsh-autosuggestions
     zsh-syntax-highlighting
     extract
)
```
>zsh报`not found theme powerlevel10k`之类的信息，打开`~/.zshrc`文件，设置`ZSH_THEME="powerlevel10k/powerlevel10k"`

## Nvidia显卡驱动
**添加Nvidia软件源**
```shell
# Leap的显卡软件源
sudo zypper addrepo --refresh 'https://download.nvidia.com/opensuse/leap/$releasever' NVIDIA
# Tumbleweed的显卡软件源
sudo zypper addrepo --refresh https://download.nvidia.com/opensuse/tumbleweed NVIDIA
```
**获取架构信息**
```shell
lspci | grep VGA
```
**驱动版本:**
* GeForce 700 系列（Kepler）及更高版本，即 NVIDIA 于 2013 年 4 月后发售的显卡，安装`nvidia-video-G06`
*  GeForce 600 系列，安装`x11-video-nvidiaG05`
* GTX4xx/5xx Fermi 系列，安装`x11-video-nvidiaG04`
**安装显卡驱动**
```shell
zypper install nvidia-video-G06
```
**重启电脑`reboot`**
**使用`nvidia-smi`命令**
```shell
zypper install nvidia-compute-utils-G06
```
>对于同时用于核显和独享的电脑，显卡驱动安装之后重启电脑可能会黑屏，使用`Ctrl + Alt +F3`进入命令行界面

**切换显卡**
```shell
# 切换独享
sudo prime-select nvidia
# 切换核显
sudo prime-select intel
```
> 电脑黑屏能看见鼠标使用`prime-select boot intel`

**注销电脑**
```shell
reboot
```

# 开发配置
## Git
```shell
# 安装GIT
sudo zypper install git
```
**Git配置**
```shell
# 设置git代理,只对github.com使用代理，其他仓库不走代理：
git config --global http.https://github.com.proxy http://127.0.0.1:2080
git config --global https.https://github.com.proxy http://127.0.0.1:2080

取消github代理：
git config --global --unset http.https://github.com.proxy
git config --global --unset https.https://github.com.proxy

# 添加个人信息：
git config --global user.email "2063074967@qq.com" # 改成你的邮箱
git config --global user.name "lyj" # 改成你的名字

# 记住密码
git config --global credential.helper store

# GitHub生成秘钥
ssh-keygen -t rsa -b 4096 -C "lyj404@qq.com"

# 创建或编辑 ~/.ssh/config 文件，添加以下内容，来简化ssh认证流程
IdentityFile ~/.ssh/id_rsa # 指定了默认的GitHub私钥文件位置

# 或者通过将密钥添加到 SSH 代理来简化认证流程
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

## VSCode
```shell
#导入密钥
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
#添加软件源
sudo zypper addrepo https://packages.microsoft.com/yumrepos/vscode vscode
#刷新软件源
sudo zypper refresh
#安装vscode
sudo zypper install code
```

## Nodejs
**在`/etc/profile.d`目录下创建一个配置文件**
```shell
sudo touch configuration.sh
```
**配置node环境变量,在`configuration.sh`下添加以下配置**
```shell
#set nodejs enviroment
NODEJS_HOME=/home/lyj/environment/node-v18.18.2-linux-x64
PATH=$PATH:$NODEJS_HOME/bin
export NODEJS_HOME PATH
```
**更新配置**
```shell
source configuration.sh
```
**切换淘宝镜像源**
```shell
npm config set registry http://registry.npmmirror.com
```

## Java
**下载java，配置环境变量`/etc/profile.d/configuration.sh`**
```shell
#set java environment
JAVA_HOME=/home/lyj/environment/Java/jdk-17.0.9
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
PATH=$PATH:$JAVA_HOME/bin
export JAVA_HOME CLASSPATH PATH
```

## Maven
**下载Maven，配置环境变量`/etc/profile.d/configuration.sh`**
```shell
#set maven enviroment
MAVEN_HOME=/home/lyj/environment/apache-maven-3.9.5
PATH=$PATH:$MAVEN_HOME/bin
export MAVEN_HOME PATH
```
**设置阿里云镜像`/home/lyj/environment/apache-maven-3.9.5/conf/settings.xml`**
```xml
<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```
**设置本地仓库位置**
```xml
<localRepository>/home/lyj/environment/repo</localRepository>
```

## MySQL
**下载MySQL软件源**
[mysql80-community-release-sl15-8.noarch.rpm](https://repo.mysql.com//mysql80-community-release-sl15-8.noarch.rpm)
**添加MySQL软件源**
```shell
sudo zypper in ./mysql80-community-release-sl15-8.noarch.rpm
```
**开始安装MySQL**
```shell
sudo zypper in mysql-community-server
```
**启动MySQL**
```shell
# 开启MySQL服务
systemctl start mysql
# 查看MySQL服务状态
systemctl status mysql
```
**查看MySQL默认密码**
```shell
sudo grep 'temporary password' /var/log/mysql/mysqld.log
```
**登录MySQL并修改密码**
```shell
# 登录
mysql -u root -p
# 设置一个符合安全策略的密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'Root_12root';
# 调整密码等级
set global validate_password.policy=0;
# 调整密码长度
set global validate_password.length=1;
# 修改密码
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
```

## MongoDB
**导入MongoDB公钥**
```shell
sudo rpm --import https://www.mongodb.org/static/pgp/server-7.0.asc
```
**添加 MongoDB 的软件源**
```shell
sudo zypper install zypper-migration-plugin
sudo zypper ar -f https://repo.mongodb.org/zypper/suse/15/mongodb-org/4.4/x86_64/ mongodb
```
**刷新软件源并安装软件**
```shell
sudo zypper ref
sudo zypper in mongodb-org
```
**启动 MongoDB 服务**
```shell
systemctl start mongod
```
**设置开机自启**
```shell
systemctl enable mongod
```
**修改MongoDB设置**
1. 编辑 MongoDB 配置文件
```shell
sudo nano /etc/mongod.conf
```
2. 在配置文件中找到 bindIp 项，并将其修改为 0.0.0.0，这将使 MongoDB 监听所有可用 IP 地址
3. 重启MongoDB服务`sudo systemctl restart mongod`
**修改防火墙设置**
```shell
# 查看防火墙对应的端口是否开启
sudo firewall-cmd --query-port=27017/tcp
# 打开防火墙对应的端口
sudo firewall-cmd --add-port=27017/tcp --permanent
# 重新加载防火墙规则
sudo firewall-cmd --reload
```
**使用Navicat连接MongoDB报错如下**
```text
Cannot connect to MongoDB.
No suitable servers found: serverSelectionTimeoutMS' expired: (TLS handshake failed: error00000000:lb(0):func(0):reason(0)
```
>解决方法在Navicat中取消`使用SSL`选项

## PostgreSQL
**添加软件源并刷新**
```shell
sudo zypper addrepo https://download.postgresql.org/pub/repos/zypp/repo/pgdg-sles-15-pg17-devel.repo
sudo zypper ref
```
**安装软件**
```shell
sudo zypper in postgresql15-server
```
**初始化数据库并设置密码**
```shell
# 使用root用户身份切换到postgres用户
sudo su - postgres
# 初始化PostgreSQL, -D 参数用于指定新的数据库目录
initdb -D /var/lib/pgsql/data
# 设置密码
psql -c "ALTER USER postgres WITH PASSWORD '123456';"
# 退出postgres用户
exit
```
**启动PostgreSQL或设置开机自启**
```shell
# 启动PostgreSQL
sudo systemctl start postgresql
# 设置开机自启
sudo systemctl enable postgresql
```
**设置远程连接**
```shell
# 修改postgresql.conf
sudo nano /var/lib/pgsql/data/postgresql.conf
# 新增listen_addresses参数，默认只允许本地连接，设置为允许来自任何 IP 地址的连接
listen_addresses = '*'
# 修改pg_hba.conf
sudo nano /var/lib/pgsql/data/pg_hba.conf
# 在文件末尾添加配置，运行所有IP连接
host  all  all  0.0.0.0/0  md5
# 重新启动PostgreSQL服务以使更改生效
sudo systemctl restart postgresql
# 查找防火墙是否开放PostgreSQL对应的端口，yes是开放，no是未开放
sudo firewall-cmd --query-port=5432/tcp
# 让防火墙开放PostgreSQL对应的端口
sudo firewall-cmd --add-port=5432/tcp --permanent
# 重新加载防火墙规则
sudo firewall-cmd --reload
```

## Docker
**安装docker和docker-compose**
```shell
zypper install docker python3-docker-compose
```
**设置开机自启动**
```shell
sudo systemctl enable docker
```

## Lazygit
**openSUSE Tumbleweed**
**openSUSE Leap**
```shell
source /etc/os-release
sudo zypper ar https://download.opensuse.org/repositories/devel:/languages:/go/$VERSION_ID/devel:languages:go.repo
sudo zypper ref && sudo zypper in lazygit
```
## Jetbrains产品光标不跟随的问题
[JetBrainsRuntime-for-Linux-x64](https://github.com/RikudouPatrickstar/JetBrainsRuntime-for-Linux-x64)

# 工具安装
## Flameshot(截图工具)
**安装**
```shell
sudo zypper in flameshot
# 使用flatpak安装
flatpak install flathub org.flameshot.Flameshot
```
**配置快捷键（KDE Plasma）**
1. 获取配置文件
```shell
wget https://raw.githubusercontent.com/flameshot-org/flameshot/master/docs/shortcuts-config/flameshot-shortcuts-kde.khotkeys
```
2. 禁用默认的截图快捷键
打开 KDE 设置，找到 快捷键 ，点击 快捷键 ，然后在右侧菜单栏中删除 Spectale

3. 导入设置
在原页面中，点击左侧的 自定义快捷键 ，再点击 编辑 ，然后点击 导入，保存，并注销重新登陆。

# 系统急救
按`Ctrl + Alt + F1`进入内核终端页面
按`Ctrl + Alt + F3`进入命令行界面
按`Ctrl + Alt + F7`切换会桌面环境

**konsole**设置方案出错之后可以使用以下命令恢复默认配置:
```shell
mv ~/.config/konsolerc ~/.config/konsolerc_backup
```

# OpenSUSE常用网址
**查找软件**
[OpenSUSE software](https://software.opensuse.org/)
**OpenSUSE wiki**
[OpenSUSE wiki](https://zh.opensuse.org/%E9%A6%96%E9%A1%B5)
