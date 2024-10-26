---
title: Linux日常使用记录
date: 2024-05-19 16:49:49
tags: Debian
categories: Linux
keywords: Debian
cover: https://s21.ax1x.com/2024/03/14/pFcL7lj.png
description: 该文章记录了日常使用Linux过程中一些我容易遗忘的内容
---
# 更换当前语言
**列出当前安装的语言：**
```shell
locale -a
```
**生成语言支持**
```shell
sudo locale-gen en_US.UTF-8
```
## 更改语言环境变脸
**临时更改：**
```shell
export LANG=en_US.UTF-8
```
**永久更改（需要`/etc/default/locale` 文件）：**
```shell
sudo nano /etc/default/locale
# 在文件中添加一下内容，注释其他的语言
LANG=en_US.UTF-8
```
## 环境变量
```shell
export PATH=$HOME/.local/bin:$PATH
```
`:$PATH`：作用是在保留原有的PATH内容，并在PATH内容的签名添加相关的依赖
## 硬链接和软链接

### 硬链接的特点
1. **共享相同的inode：** 硬链接指向同一个inode，即同一个文件数据块。删除其中一个硬链接不会影响文件数据，只有当所有硬链接都被删除时，文件数据才会被删除
2. **无法夸文件系统：** 硬链接只能在同一个文件系统内创建，不能跨文件系统
3. **不能链接目录：** 普通用户不能创建指向目录的硬链接（防止文件系统的混乱）
#### 硬链接的相关命令
```shell
# 创建硬链接
ln file1 hardlink_to_file1
# 取消硬链接
rm hardlink_to_file1
# 验证硬链接（能看到两个文件有相同的inode）
ls -li file1 hardlink_to_file1
```
> 删除这个硬链接不会影响 `hardlink_to_file1` 的数据，除非 `hardlink_to_file1` 没有其他硬链接。

### 软链接的特点
1. **指向路径：** 软链接是一个独立的文件，包含另外一个文件或目录的路径。它们类似于Windows的快捷方式
2. **可以跨文件系统：** 软链接可以指向不同的文件系统中的文件或目录
3. **可以链接目录：** 可以创建指向目录的软链接
4. **相对和绝对路径：** 软链接可以使用相对或绝读路径
#### 软链接相关的命令
```shell
# 创建软链接
ln -s /path/to/file1 symlink_to_file1
# 取消软链接
rm symlink_name
# 验证软链接
ls -l file1 symlink_to_file1
```
## inode
在Linux和其他的Unix类操作系统中，`inode（索引节点）`是文件系统的一个重要概念，用于描述文件的元数据和存储位置。每个文件和目录都有一个对应的inode，存储该文件或目录的相关信息

**inode包含的信息：**
1. **文件类型：** 如普通文件、目录、符号链接等
2. **权限和模式：** 文件的读、写、执行权限，以及文件类型信息
3. **链接计数：** 指向该inode的硬链接数量
4. **所有者和组：** 文件所有者的用户ID和组ID
5. **文件大小：** 以字节为单位的文件大小
6. **时间戳：** 包含访问时间、修改时间、改变时间
7. **数据块指针：** 指向实际存储文件数据的数据块指针列表

> `inode`不包含文件名，文件名和inode是通过目录向联系在一起的。目录将文件名映射到相应的inode

### inode的相关命令操作
**查看文件的inode编号：**
```shell
ls -i Java面试宝典\(牛客网\).pdf
```
输出结果如下：
```text
5509495 'Java面试宝典(牛客网).pdf'
```
**查看inode信息：**
```shell
stat Java面试宝典\(牛客网\).pdf
```
输出结果如下：
```text
  File: Java面试宝典(牛客网).pdf
  Size: 14643112  	Blocks: 28600      IO Block: 4096   regular file
Device: 8,2	Inode: 5509495     Links: 1
Access: (0600/-rw-------)  Uid: ( 1000/     lyj)   Gid: ( 1000/     lyj)
Access: 2024-05-19 15:38:36.904412714 +0800
Modify: 2024-04-03 13:22:56.879405504 +0800
Change: 2024-04-03 13:22:56.895405189 +0800
 Birth: 2024-04-03 13:22:56.115420560 +0800
```

> 文件系统的inode数量是固定的。如果`inode`数量用尽了，即使磁盘空间还有剩余，也无法新建文件，且不同的文件系统有不同的`inode`结构和管理方式，如 ext4、XFS、Btrfs 等
## 重定向操作符>
**作用：** `>` 将命令的标准输出（stdout）重定向到一个文件。如果该文件已经存在，他会被覆盖。如果文件不存在则会创建一个新文件。
```shell
echo "Hello World" > file2
```
该命令的作用：
1. `echo "Hello World"`：`echo`命令用于在终端打印字符串
2. `>`：重定向操作符，将`echo`命令的输出重定向到文件
3. `file2`：目标文件名。如果文件存在，内容则被覆盖，不存在会创建新的文件
#### 其他重定向操作符
**追加操作符`>>`：** 将输出追加到文件末尾，而不是覆盖文件内容
```shell
echo "Hello Again" >> file2
```
该命令将会在`file2`文件的末尾追加"Hello Again"

**输入重定向 `<`：** 将文件内容作为命令的输入
```shell
sort < file2
```
该命令会将 `file2` 的内容作为 `sort` 命令的输入并进行排序

**错误重定向 `2>`：** 将标准错误（stderr）重定向到文件
```shell
ls non_existent_file 2> error.log
```
该命令会将 `ls` 命令尝试列出一个不存在文件的错误信息重定向到 `error.log` 文件中

**合并输出和错误`&>`：** 将标准输出和标准错误重定向到一个文件
```shell
command &> output.log
```
该命令会将 `command` 的所有输出（包括错误信息）重定向到 `output.log` 文件

# Linux系统常用命令
## 文件和目录管理
**`ls`**：列出目录内容
```shell
ls -l   # 以详细列表的形式列出文件
ls -a   # 显示所有文件，包括隐藏文件
```
**`cd`**：更改目录
```shell
cd /path/to/directory   # 切换到指定目录
cd ..                   # 返回上一级目录
```
**`pwd`**：显示当前工作目录
```shell
pwd   # 显示当前所在的目录路径
```
**`cp`**：复制文件或目录
```shell
cp source.txt destination.txt    # 复制文件
cp -r source_dir/ destination/   # 复制目录
```
**`mv`**：移动或重命名文件或目录
```shell
mv oldname.txt newname.txt   # 重命名文件
mv file.txt /path/to/newdir/ # 移动文件到新目录
```
**`rm`**：删除文件或目录
```shell
rm file.txt       # 删除文件
rm -r directory/  # 递归删除目录及其内容
```
**`mkdir`**：创建目录
```shell
mkdir new_directory   # 创建新目录
mkdir -p path/to/new_directory  # 递归创建目录
```
**`touch`**：创建空文件或更新文件的时间戳
```shell
touch newfile.txt
```
**`find`**：用于在目录树中查找文件和目录
* *`-name`**：按文件名查找
```shell
find /path/to/search -name "*.txt"   # 查找所有以 .txt 结尾的文件
```
* **`-type`**：按文件类型查找
```shell
find /path/to/search -type d   # 查找所有目录
find /path/to/search -type f   # 查找所有普通文件
```
* **`-size`**：按文件大小查找
```shell
find /path/to/search -size +1M   # 查找大于 1MB 的文件
```
* **`-mtime`**：按修改时间查找
```shell
find /path/to/search -mtime -7   # 查找 7 天内修改过的文件
```
* **`-exec`**：对找到的文件执行命令
```shell
find /path/to/search -name "*.log" -exec rm {} \;   # 查找并删除所有 .log 文件
```
**`grep`** 命令：用于搜索文件中的文本模式
* **`-i`**：忽略大小写
```shell
grep -i "pattern" file.txt   # 搜索时忽略大小写
```
* **`-r`**：递归搜索目录
```shell
grep -r "pattern" /path/to/dir   # 递归搜索目录中的文件
```
* **`-v`**：排除匹配项
```shell
grep -v "pattern" file.txt   # 显示不包含 "pattern" 的行
```
* **`-n`**：显示匹配行的行号
```shell
grep -n "pattern" file.txt   # 显示包含 "pattern" 的行及其行号
```
* **`-l`**：显示包含匹配项的文件名
```shell
grep -l "pattern" /path/to/dir/*   # 仅显示包含匹配项的文件名
```
## 文件查看和编辑
**`cat`**：显示文件内容
```shell
cat file.txt
```
**`more`** 和 **`less`**：分页查看文件内容
```shell
more file.txt   # 从头开始显示文件内容
less file.txt   # 支持上下滚动查看文件内容
```
**`head`** 和 **`tail`**：查看文件的头部或尾部内容
```shell
head -n 10 file.txt   # 查看文件的前 10 行
tail -n 10 file.txt   # 查看文件的后 10 行
```
**`nano`** 或 **`vi`** / **`vim`**：编辑文件
```shell
nano file.txt    # 使用 nano 编辑器打开文件
vim file.txt     # 使用 vim 编辑器打开文件
```
## 系统信息和管理
**`df`**：显示磁盘空间使用情况
```shell
df -h   # 以人类可读的格式显示磁盘使用情况
```
**`du`**：显示文件或目录的大小
```shell
du -sh /path/to/directory   # 递归显示目录大小
```
**`free`**：显示内存使用情况
```shell
free -h   # 以人类可读的格式显示内存使用情况
```
**`top`** 和 **`htop`**：实时显示系统资源和进程信息
```shell
top    # 显示实时的系统资源使用信息
```
**`ps`**：显示当前进程信息
```shell
ps -aux   # 显示所有用户的所有进程
```
* `-e`或`-A`：显示所有进程
* `-f`：显示完整格式
* `-u`：按用户过滤进程
* `-aux`：显示所有进程，包括不属于终端的进程，并显示详细信息
**`kill`** 和 **`killall`**：终止进程
```shell
kill PID         # 终止指定 PID 的进程
killall process  # 终止指定名称的进程
```
* `kill -9`：强制终止进程
*  `kill -6`：让进程异常终止
* `kill -l`：列出所有信号名称
*  `kill -15`：发送默认终止信号（较为优雅地终止）
**`shutdown`** 和 **`reboot`**：关机和重启系统
```shell
sudo shutdown -h now   # 立即关机
sudo reboot            # 重启系统
```
## 网络管理
**`ifconfig`** 或 **`ip`**：查看和配置网络接口
```shell
ifconfig
ip addr show
```
**`ping`**：测试网络连通性
```shell
ping google.com
```
**`curl`** 和 **`wget`**：下载文件或测试 HTTP 请求
```shell
curl http://example.com
wget http://example.com/file.zip
```
**`netstat`** 或 **`ss`**：查看网络连接和端口使用情况
```shell
netstat -tuln   # 显示所有监听的端口
ss -tuln        # 类似于 netstat，但输出更快
```
## 权限和用户管理
**`chmod`**：更改文件权限
```shell
chmod 755 file.txt    # 设置文件权限为 755
chmod +x script.sh    # 给脚本添加可执行权限
```
- **文件所有者（7）**：`rwx`（读、写、执行）
- **组用户（5）**：`r-x`（读和执行，没有写权限）
- **其他用户（5）**：`r-x`（读和执行，没有写权限）
**`chown`**：更改文件或目录的所有者
```shell
chown user:group file.txt   # 修改文件的所有者
```
**`sudo`**：以超级用户权限执行命令
```shell
sudo command   # 使用 sudo 以 root 权限运行命令
```
**`useradd`** 和 **`usermod`**：管理用户
```shell
sudo useradd newuser         # 添加新用户
sudo usermod -aG sudo user   # 将用户添加到 sudo 组
```
## 压缩和解压缩
**`tar`**：打包和解压 tar 文件
```shell
tar -czvf archive.tar.gz /path/to/directory   # 打包并压缩
tar -xzvf archive.tar.gz                      # 解压
```
* `-c`：创建新的`tar`文件
* `-x`：解压`tar`文件
* `-z`：使用`gzip`压缩或解压
* `-j`：使用`bzip2`压缩或解压
* `-f`：指定文件名
* `-v`：显示详细信息
**`zip`** 和 **`unzip`**：压缩和解压 zip 文件
```shell
zip -r archive.zip /path/to/directory   # 压缩文件
unzip archive.zip                       # 解压 zip 文件
```
* `zip -r`：递归压缩目录
* `zip -e`：为压缩文件设置密码
* `zip -x`：排除指定文件
**`gzip`** 和 **`gunzip`**：压缩和解压 gzip 文件
```shell
gzip file.txt    # 压缩文件
gunzip file.gz   # 解压文件
```

# nano
## nano基本操作快捷键
* **保存文件：** `Ctrl + o`
* **退出nano：** `Ctrl + x`
* **剪贴文本：** `Ctrl + k`
* **粘贴文本：** `Ctrl + u`
* **选择文本：** `Ctrl + Shift + 6`
* **撤销：** `Alt + u`
* **重做：** `Alt + e`
* **移动到行首：** `Ctrl + a`
* **移动到行尾：** `Ctrl + e`
* **向下翻页：** `Ctrl + y`
* **向下翻页：** `Ctrl + v`
* **移动到文件开头：** `Ctrl + home`
* **移动到文件结尾：** `Ctrl + end`
* **搜索文本：** `Ctrl + w`
* **搜索并替换文本：** `Ctrl + \`
## nano常用配置
nano全局配置文件路径`/etc/nanorc` ，用户配置路径`~/.nanorc`
```bash
# 显示行号
set const
# 设置 Tab 大小
set tabsize 4
# 将tab转为空格
set tabstospaces
# 显示光标位置
set cursorpos
# 高亮当前行
set smarthome
# 启用鼠标选择和复制
set mouse
# 禁用自动换行
set nowrap
# 保存时修剪行尾空白
set trimblanks
# 禁用备份文件
set backup
# 启用语法高亮
include "/usr/share/nano/*.nanorc"
```
