---
title: Debian编译Emacs
date: 2024-04-28 17:56:25
tags: Emacs
categories: Linux
keywords: Debian
cover: https://s21.ax1x.com/2024/04/28/pkifcYn.jpg
description: Emacs 是一个文本编辑器系列，包含有多个分支，其中最主流的一支是 GNU Emacs，大多数情况下所说的 Emacs 都是指 GNU Emacs。Emacs 这一名字最早来源于 “Editor MACroS”，后来也有人称它集合了五个主要功能键的首字母 Esc、Meta、Alt、Ctrl、Shift。
---
# 安装依赖
Debian编译Emacs需要一些依赖，下载方法如下：
```shell
sudo apt build-dep emacs
sudo apt install libtree-sitter-dev
```
## 下载和解压Emacs
[Emacs29.3](https://mirror.its.dal.ca/gnu/emacs/emacs-29.3.tar.xz)下载地址
```shell
# 下载
wget https://mirror.its.dal.ca/gnu/emacs/emacs-29.3.tar.xz
# 解压
tar xvf emacs-29.3.tar.xz
```
# 编译Emacs
```shell
# 生成配置脚本和Makefile
./autogen.sh
# 配置编译选项
./configure --with-native-compilation=aot --with-native-compilation --with-json --with-tree-sitter CC=gcc-12
# 设置8个核心并行编译
make -j 8
# 查看Emacs版本
src/emacs --version
# 快速启动测试一下Emacs
src/emacs -Q
# 将编译后的Emacs安装到指定目录
sudo make install
```
> 如果使用的是Wayland，可以需要添加`--with-pgtk`选项以获得更好的兼容性

* `--without-compress-install`是在安装时不要压缩 Emacs 的文档和信息文件。这可以使安装过程更快，但会增加磁盘空间的使用
* `--with-native-compilation` 启用 Emacs 的原生编译支持，`aot` 指定了原生编译的具体类型
* `--with-json` 允许 Emacs 支持 JSON 文件的读写操作
* `--with-tree-sitter` 启用对 Tree-sitter 语法高亮库的支持
* `CC=gcc-12` 指定使用 `gcc-12` 作为编译器来编译 Emacs
# 卸载Emacs
```shell
# 卸载Emacs
sudo make uninstall
# 清理构建文件
make clean 
make distclean
```
* `make clean` 清除编译过程中生成的文件，但不删除配置文件和日志
* `make distclean` 更彻底地清理，包括删除配置文件和日志，回到初始状态
