---
title: Debian日常使用记录
date: 2024-05-19 16:49:49
tags: Debian
categories: Linux
keywords: Debian
cover: https://s21.ax1x.com/2024/04/16/pFxKR2t.png
description: Debian是完全由自由软件组成的类UNIX操作系统，其包含的多数软体使用GNU通用公共许可协议授权，并由Debian计划的参与者组成团队对其进行打包、开发与维护。
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

# 修复Linux系统下WPS打不开PDF
Linux系统下打不开PDF是缺少了一个依赖文件`libtiff.so.5`
**解决方法如下：**
```shell
sudo ln -s /usr/lib/x86_64-linux-gnu/libtiff.so.6.0.0 /usr/lib/x86_64-linux-gnu/libtiff.so.5
```
**查看是否链接成功**
```shell
ls /usr/lib/x86_64-linux-gnu | grep libtiff
# 输出结果如下
libtiff.a
libtiff.so
libtiff.so.5
libtiff.so.6
libtiff.so.6.0.0
libtiffxx.a
libtiffxx.so
libtiffxx.so.6
libtiffxx.so.6.0.0
```
