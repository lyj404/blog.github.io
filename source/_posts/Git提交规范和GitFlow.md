---
title: Git提交规范和GitFlow
date: 2025-01-06 20:38:40
tags: Git
categories: BackEnd
keywords: Git提交规范和GitFlow
cover: https://s21.ax1x.com/2025/01/06/pE9wOhD.png
description: 讲述如何使用 Git 提交规范和 Git Flow 工作流来有效管理代码版本控制。通过遵循标准化的提交信息格式和结构化的分支策略，开发团队可以提高代码的可维护性和协作效率。
---
# Git提交规范
在使用`git` 提交代码的时候，由于每个人都有自己的书写风格，导致`git log`很混乱，不利于阅读和维护，因此形成了一套通用`git`提交规范。
**格式：**
```text
type(scope) : subject
```
`type`是必须项，主要用来描述`commit`的类别，且只允许使用以下的标示：
* feat：新功能（feature）
	* 用于提交新功能
	* 例如：`feat: 添加用户注册功能`
* fix：修复bug
	* 用于提交bug修复
	* 例如：`fix：修复登录不成功的问题`
* docs：
	* 用于提交文档相关的修改
	* 例如：`docs：更新README`
* style：代码格式改变
	* 用于提交仅格式化、标点符号、空白等不影响代码运行和逻辑的变更
	* 例如：`style：删除多余空格`
* refactor：代码重构
	* 用于提交某个已有功能重构
	* 例如：`refactor：重构用户登录功能`
* perf：性能优化
	* 用于提交提升程序性能的代码修改
	* 例如：`perf： 优化首页加载速度`
* test：测试相关的操作
	* 用于提交测试相关的内容
	* 例如：`test：增加用户登录的单元测试`
* build：用于描述与项目构建系统相关的更改
	* 用于提交与项目构建系统相关的更改
	* 例如：`build：jdk8升级JDK17`
* revert：撤销上次的commit
	* 用于提交撤销的相关操作
	* 例如：`revert：撤销登录逻辑的更改`
* ci：持续集成相关的配置的更改
	* 用于提交CI相关的修改
	* 例如：`ci：修改GitHub Action配置`
* chore：构建过程或辅助工具的更改
	* 用于提交构建过程、辅助工具等相关内容的修改
	* 例如：`chore：fastjson升级fastjson2`
`scoop`是可选项，用于说明`commit`影响的范围，如：controller层、service层、dao层等等。
`subject`是必须项，主要是对`commit`进行简短描述，且不能超过50个字符。
## 通过工具规范提交
### 模板文件
创建一个`.gitmessage.txt`文件，则可自定义`git`提交模板
例如：
```text
reason: 
resolve: 
```
在模板文件定义之后，还需执行以下命令：
```shell
git config --global commit.template /xx/xx/.gitmessage.txt
```
> 使用自定义模板提交方式，不使用`git commit -m "xxx"`，而是使用`git commit`，就会自动调用模板

### commitizen工具
`Commitizen`是一个用于帮助我们规范化Git提交信息的工具，它提供了一种交互式的方式来生成符合提交规范的提交信息
**安装：**
```shell
npm install -g commitizen
```
初始化`Commitizen`配置：
```shell
# npm
commitizen init cz-conventional-changelog --save-dev --save-exact

# yarn
commitizen init cz-conventional-changelog --yarn --dev --exact

# pnpm
commitizen init cz-conventional-changelog --pnpm --save-dev --save-exact
```
使用方式：`git cz`
>不是前端项目，则需要在项目中创建`package.json`文件

# GitFlow
GitFlow是一种功能强大的`Git分支模型`，目的在于帮助团队管理软件开发过程中的复杂性。由于每个组织或个人都有自己的`git`管理流程，随着时间的发展，在不停的实践中，逐渐形成了一些最佳实践的`git`管理流程，也就是我们所说的`Git分支模型`。
**常见的分支模型有：**
1. Git Flow
2. GitHub Flow
3. GitLab Flow
4. TrunkBased
5. One Flow
6. Aone Flow
>其中Git Flow是最早诞生、并且得到广泛采用的

## Git Flow是什么？
**Git Flow** 是一种Git分支管理模型，它定义了一个围绕项目发布的严格分支建立模型。这个分支模型是团队成员需要遵守的代码管理方案，目的是通过规范化的流程，使得产品、开发与测试等各个部门更高效地协同工作。
## Git Flow核心概念
Git Flow的核心是分支管理。它将分支分成两类：主分支和辅助分支。主分支包括`master`和`develop`，而辅助分支包含`feature`、`release`、和`hotfix`分支。每种分支都有特定的作用和规则。
## Git Flow常用分支及说明
| 分支名称       | 说明                                                                                                |
| ---------- | ------------------------------------------------------------------------------------------------- |
| Production | 生产分支，即**Master**分支。只能从其他分支合并，不能直接修改                                                               |
| Release    | 发布分支，基于**Develop**分支创建，等发布完成合并到**Develop**和**Production**分支去                                      |
| Develop    | 主开发分支，包含了所有要发布到下一个**Release**的代码，该分支主要用于合并其他分支的代码                                                 |
| Feature    | 新功能分支，基于**Develop**分支创建，用于开发新功能，等开发完毕将合并到**Develop**分支                                            |
| Hotfix     | 修复分支，基于**Production**分支创建，在bug修复完毕之后，将合并到**Production**和**Develop**分支，同时会在**Production**分支上打一个tag |
## Git Flow工作流程
**Git Flow分支模型的工作流程如下图所示：**
[![gitflow工作流程.png](https://s21.ax1x.com/2025/01/06/pE9w5c9.png)](https://imgse.com/i/pE9w5c9)
>`feature`分支属于本地分支，其余的分支均是云端分支，且`hotfix`和`release`在将代码合并到`master`和`develop`分支之后，将会删除自身
