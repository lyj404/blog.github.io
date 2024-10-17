---
title: 解决Linux下WPS存在的问题
date: 2024-09-29 20:41:59
tags: Endeavour OS
categories: Linux
keywords: WPS
cover: https://s21.ax1x.com/2024/09/29/pA1gr6J.jpg
description: 相较于Windows下的WPS，Linux下的 WPS存在的较多的问题，如字体确实，PDF功能不能使用，PDF文字缺失等问题。
---
## WPS
**安装WPS之后，需要将windows中的字体拷贝到Linux系统下，否则将会导致WPS出现字体缺失问题**
```shell
# 创建字体目录
sudo mkdir /usr/share/fonts/WindowsFonts

# 复制字体
sudo cp Fonts/* /usr/share/fonts/WindowsFonts

# 赋权
sudo chmod 644 /usr/share/fonts/WindowsFonts/*

# 刷新
fc-cache -f
```

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
> 在Linux操作系统下WPS不能将word转换成PDF也是因为缺少`libtiff.so.5`

# wps将word转换成PDF，PDF出现文字缺失问题
在Linux使用WPS将word文档转换成PDF，使用Okular打开PDF出现文字确实问题，且发送到手机微信，在手机微信上打开同样出现文字确实问题
**解决方法：** 安装`poppler-data`，`poppler-data` 是一个数据包，它提供了字体和其他信息，供 `Poppler` 使用。`Poppler` 是一个开源 PDF 渲染库，用于解析和渲染 PDF 文档。
**arch系列软件安装`poppler-data`的方法：**
```shell
sudo pacman -S poppler-data
```

**如还出现问题可能是Linux系统中缺少一些中文特定的字体，从而导致文档显示不正常。**
```shell
# ttf-wps-fonts 是 WPS 专用字体包
sudo pacman -S ttf-wps-fonts
sudo pacman -S noto-fonts-cjk
```
> 最后则需要在WPS中选择已安装的字体（如：Noto Sans CJK SC）
