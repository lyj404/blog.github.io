---
title: Shell编程
date: 2023-08-27 16:47:56
tags: Shell
categories: Linux
keywords: Shell编程
description: Shell既是一种命令语言，又是一种程序设计语言，在中文中解释“外壳”的意思，就是操作系统的外壳。工作中我们通过shell命令来操作和控制操作系统，在Linux操作系统中的Shell命令就包括touch、cd、pwd、mkdir等等。
---
# Shell
## 什么是Shell？
shell编程就是对一堆Linux命令的逻辑化处理。

## Shell编程Hello World
1. 新建文件`touch helloworld.sh`
2. 添加执行权限`chmod +x helloworld.sh`
3. 编辑sh脚本
```shell
#!/bin/bash
echo "helloworld"
```
>在shell种`#`表示注释。shell的第一行比较特殊，一般都会以`#!`开头来指定使用的shell类型

**shell的类型：**
* bash
* zsh
* fish
4. 运行脚本`./helloworld.sh`。运行shell脚本需要添加`./`的路径，直接使用`helloworld.sh`，Linux系统回去PATH里寻找，一般只有`/bin`, `/sbin`，`/usr/bin`，`/usr/sbin`在PATR里，因此直接使用`helloworld.sh`是找不到命令的

## Shell的变量
**Shell的变量一般分为三种：**
1. **自定义变量**：就在当前shell种有效，其他shell中无效。
2. **Linux定义的环境变量：** 例如`PATH`，`HOME`，使用`env`命令可以查看所有的环境变量，而`set`命令既可以查看环境变量也可以查看自定义变量。
3. **Shell变量：** Shell变量是有Shell程序设置的特殊变量。Shell变量中有一部分是环境变量，一部分是局部变量
**常用的环境变量：**
>PATH 决定了 shell 将到哪些目录中寻找命令或程序  
  HOME 当前用户主目录  
  HISTSIZE 　历史记录数  
  LOGNAME 当前用户的登录名  
  HOSTNAME 　指主机的名称  
  SHELL 当前用户 Shell 类型  
  LANGUAGE 　语言相关的环境变量，多语言可以修改此环境变量  
  MAIL 　当前用户的邮件存放目录  
  PS1 　基本提示符，对于 root 用户是#，对于普通用户是$

### 使用Linux定义的环境变量
1. `echo $HOME`：查看当前用户目录
2. `echo $SHELL` 查看当前用户的Shell类型

### 使用自定义变量
```shell
#!/bin/bash
hello="helloworld"
echo $hello
```

**Shell变量名的命名规范：**
* 命名只能使用英文字母，数字和下划线，首字母不能以数字开头，但可以使用下划线开头
* 中间不能有空格，但可以使用下划线
* 不能使用标点符号
* 不能使用bash的关键字

### Shell的字符串
Shell的字符串可以是单引号，也可以是双引号。在单引号中所有的特殊字符(`$`、`\`等)都没有特殊含义。在双引号中，除了"$"、"\"、反引号和感叹号（需开启 `history expansion`），其他的字符没有特殊含义。
**单引号：**
```shell
#!/bin/bash
name='lyj'
hello='Hello, My name is $name!'
echo $hello
```
输出结果
```text
Hello, My name is $name!
```
**双引号：**
```shell
#!/bin/bash
name='lyj'
hello="Hello, My name is $name!"
echo $hello
```
输出结果
```text
Hello, My name is lyj!
```

#### 字符串常见操作
##### 拼接字符串
```shell
#!/bin/bash
name="lyj"
# 使用双引号拼接
greeting="hello, "$name" !"
greeting_1="hello, ${name} !"
echo $greeting  $greeting_1
# 使用单引号拼接
greeting_2='hello, '$name' !'
greeting_3='hello, ${name} !'
echo $greeting_2  $greeting_3
```

##### 获取字符串的长度
```shell
#!/bin/bash
#获取字符串长度
name="lyj"
# 方式一
echo ${#name} #输出 10
# 方式二
expr length "$name";
```
>使用 `expr`命令时，表达式中的运算符左右必须包含空格，如果不包含空格，将会输出表达式本身。
```shell
expr 5+1   // 直接输出 5+1
expr 5 + 1       // 输出 6
```
>对于某些运算符，还需要我们使用符号`\`进行转义，否则就会提示语法错误。
```shell
expr 5 * 1       // 输出错误
expr 5 \* 1      // 输出5
```

##### 截取字符串
```shell
#从字符串第 1 个字符开始往后截取 10 个字符
str="abcdefghij is a great man"
echo ${str:0:10} #输出:abcdefghij
```
```shell
#!bin/bash
#author:amau

var="https://www.runoob.com/linux/linux-shell-variable.html"
# %表示删除从后匹配, 最短结果
# %%表示删除从后匹配, 最长匹配结果
# #表示删除从头匹配, 最短结果
# ##表示删除从头匹配, 最长匹配结果
# 注: *为通配符, 意为匹配任意数量的任意字符
s1=${var%%t*} #h
s2=${var%t*}  #https://www.runoob.com/linux/linux-shell-variable.h
s3=${var%%.*} #http://www
s4=${var#*/}  #/www.runoob.com/linux/linux-shell-variable.html
s5=${var##*/} #linux-shell-variable.html
```

### Shell的数组
bash只支持一维数组不支持多维数据。
```shell
#!/bin/bash
array=(1 2 3 4 5);
# 获取数组长度
length=${#array[@]}
# 或者
length2=${#array[*]}
#输出数组长度
echo $length #输出：5
echo $length2 #输出：5
# 输出数组第三个元素
echo ${array[2]} #输出：3
unset array[1]# 删除下标为1的元素也就是删除第二个元素
for i in ${array[@]};do echo $i ;done # 遍历数组，输出：1 3 4 5
unset array; # 删除数组中的所有元素
for i in ${array[@]};do echo $i ;done # 遍历数组，数组元素为空，没有任何输出内容
```

## Shell的基本运算符
### 算数运算
[![算数运算](https://z1.ax1x.com/2023/09/16/pPftC1P.png)](https://imgse.com/i/pPftC1P)
```shell
#!/bin/bash
a=3;b=3;
val=`expr $a + $b`
#输出：Total value : 6
echo "Total value : $val"
```
### 关系运算
[![关系运算](https://z1.ax1x.com/2023/09/16/pPfa1nf.png)](https://imgse.com/i/pPfa1nf)
```shell
#!/bin/bash
score=90;
maxscore=100;
if [ $score -eq $maxscore ]
then
   echo "A"
else
   echo "B"
fi
```
### 逻辑运算
[![逻辑运算](https://z1.ax1x.com/2023/09/16/pPftSfI.png)](https://imgse.com/i/pPftSfI)
```shell
#!/bin/bash
a=$(( 1 && 0))
# 输出：0；逻辑与运算只有相与的两边都是1，与的结果才是1；否则与的结果是0
echo $a;
```

### 布尔运算
[![布尔运算](https://z1.ax1x.com/2023/09/16/pPftP6f.png)](https://imgse.com/i/pPftP6f)

### 字符串运算
[![字符串运算](https://z1.ax1x.com/2023/09/16/pPfaQjP.png)](https://imgse.com/i/pPfaQjP)
```shell
#!/bin/bash
a="abc";
b="efg";
if [ $a = $b ]
then
   echo "a 等于 b"
else
   echo "a 不等于 b"
fi
```

### 文件测试运算
[![文件测试运算](https://z1.ax1x.com/2023/09/16/pPft9pt.png)](https://imgse.com/i/pPft9pt)
```shell
#!/bin/bash
file="/usr/learnshell/test.sh"
# 判断文件是否可读
if [ -r $file ]
# 判断文件是否可写
if [ -w $file ]
```

## Shell的流程控制
### if条件语句
shell的if条件语句不能包含空语句
```shell
#!/bin/bash
a=3;
b=9;
if [ $a -eq $b ]
then
   echo "a 等于 b"
elif [ $a -gt $b ]
then
   echo "a 大于 b"
else
   echo "a 小于 b"
fi
```

### for循环语句
**输出列表的数据**
```shell
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done
```

**产生十个随机数**
```shell
#!/bin/bash
for i in {0..9};
do
   echo $RANDOM;
done
```

**输出一到五**
```shell
#!/bin/bash
length=5
for((i=1;i<=length;i++));do
    echo $i;
done;
```

### while语句
```shell
#!/bin/bash
int=1
while(( $int<=5 ))
do
    echo $int
    let "int++"
done
```

## Shell的函数
### 没有参数和返回值的函数
```shell
#!/bin/bash
hello(){
    echo "这是我的第一个 shell 函数!"
}
echo "-----函数开始执行-----"
hello
echo "-----函数执行完毕-----"
```

### 有返回值的函数
**输入两个数字相加并返回结果:**
```shell
#!/bin/bash
funWithReturn(){
    echo "输入第一个数字: "
    read aNum
    echo "输入第二个数字: "
    read anotherNum
    echo "两个数字分别为 $aNum 和 $anotherNum !"
    return $(($aNum+$anotherNum))
}
funWithReturn
echo "输入的两个数字之和为 $?"
```

### 有参数的函数
```shell
#!/bin/bash
funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73
```