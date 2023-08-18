---
title: .gitignore文件的配置使用
date: 2024-03-02 17:16:07
tags: Git
categories: BackEnd
keywords: .gitignore
description: .gitignore文件的作用是告诉Git哪些文件或目录不需要纳入版本控制。当您在项目中创建.gitignore文件并列出要忽略的文件或目录时，Git会在执行各种操作（如git status、git add、git commit等）时忽略这些被指定的文件或目录。
---
在使用Git的过程中，有些文件比如说日志、临时文件、编译的中间文件文件等不需要提交到Git仓库，这个时候需要设置对应的忽略规则，来忽略这些文件的提交。使用场景：比如说在使用`git add .`命令的时候不小心把不想提交的文件添加到缓存中去了，虽然可以使用`git reset HEAD example.txt
` ，又或者是使用`git add 具体文件`的方式来避免将不想要提交的文件添加到暂存区，但是这样终归没有直接使用`git add .`方便，这个时候就可以使用Git为我们提供`.gitignore`文件，只要在文件中申明哪些文件不需要添加到暂存区，然后在使用`git add .`命令的时候就不会被添加到暂存区了。
# 实现在Git中忽略不想提交的文件
1. 直接在项目中定义.gitignore文件，然后在.gitignore文件中定义要忽略的文件
2. 在Git项目的设置中指定忽略文件，在`.git/info/exclude`文件中写入要忽略的文件
3. 定义Git全局的.gitignore文件，使用命令如下
	* `git config --global core.excludesfile ~/.gitignore`
# .gitignore忽略规则的优先级
由高到低的优先级如下：
1. 从命令行中读取可用的忽略规则  
2. 当前目录定义的规则  
3. 父级目录定义的规则，依次递推  
4. $GIT_DIR/info/exclude 文件中定义的规则  
5. core.excludesfile中定义的全局规则
# .gitignore的匹配语法
1. 空行：空行将被忽略。
2. 以 `#` 开头的表示为注释。
3. 斜杠 `/`：如果斜杠出现在行首，表示只匹配项目根目录；如果斜杠出现在其他位置，表示匹配任意位置。
4. `!`：感叹号用于取反，即排除某些匹配的文件或目录。
5. `*`：星号匹配零个或多个字符。
6. `?`：问号匹配一个字符。
7. `**`：双星号匹配任意子目录。
8. `/dir/`：表示忽略根目录下的`dir`目录。
9. `dir/`：表示忽略任意位置的`dir`目录。
10. `/*.txt`：表示只匹配根目录下的`.txt`文件。
11. `debug.log`：表示忽略任意位置的`debug.log`文件。
12. `**/logs/`：表示匹配所有子目录中的`logs`目录。
13. `*.log`：表示匹配所有以`.log`结尾的文件。
14. `assets/`：表示匹配名为`assets`的目录。
15. `build`：表示匹配名为`build`的文件或目录。
16. `build/`：表示只匹配名为`build`的目录。
> [gitignore](https://github.com/github/gitignore)该开源项目收集了所有可用的.gitignore模板

**Java .gitignore template**
```text
# Compiled class file
*.class

# Log file
*.log

# BlueJ files
*.ctxt

# Mobile Tools for Java (J2ME)
.mtj.tmp/

# Package Files #
*.jar
*.war
*.nar
*.ear
*.zip
*.tar.gz
*.rar

# virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml
hs_err_pid*
replay_pid*
```
# .gitignore配置不生效原因和解决
在Git中，即使在`.gitignore`中标明了要忽略的文件或目录，但如果这些文件已经被纳入版本管理，依然会出现在`git push`的目录中，并且在使用`git status`查看状态时仍然显示为被追踪的文件。这是因为Git会缓存已经纳入版本管理的文件，即使在`.gitignore`中声明了忽略路径也不会生效。

要解决这个问题，需要先将本地缓存删除，然后再进行提交操作。这样就可以确保被声明为忽略的文件不会被提交到版本库中，也不会出现在推送的目录中。
**解决方法: git清除本地缓存（改变成未track状态），然后再提交:**
```bash
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
git push -u origin master
```
或者在clone的时候手动设置不要检查特定文件的更改情况：`git update-index --assume-unchanged PATH`

> 需要注意的点：`.gitignore`只能忽略原来没有被track的文件，如果文件已经被纳入了版本库中，则修改`.gitignore`是无效的。想要`.gitignore`起作用，必须要在这些文件不在暂存区中才可以，`.gitignore`文件只是忽略没有被`staged(cached)`文件，对于已经被`staged`文件，加入ignore文件时一定要先从`staged`移除，才可以忽略。