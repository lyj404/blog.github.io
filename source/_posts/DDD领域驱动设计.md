---
title: DDD领域驱动设计
date: 2023-10-08 16:29:12
tags: Domain Driver Design
categories: BackEnd
cover: https://z1.ax1x.com/2023/10/08/pPvQNEn.jpg
description: DDD是一种软件设计思想和方法论，以领域为核心构建软件设计体系，将业务模型抽象成领域模型进行拆解和封装。
---
# Domain Driver Design
## MVC架构
在mvc架构中，主要划分为三个组件：Model（模型）、View（视图）、Controller（控制器）
**传统三层架构模型(MVC)** [![pPvQ5vD.png](https://z1.ax1x.com/2023/10/08/pPvQ5vD.png)](https://imgse.com/i/pPvQ5vD)

## DDD架构
DDD架构主要将应用分成四个层次：

1. 表现层（Presentation Layer）：负责与用户进行交互，展示数据和接收用户输入。表现层通常包括用户界面、控制器、视图和用户输入验证等组件。
2. 应用层（Application Layer）：负责协调处理用户请求，并将其转化为对领域层的操作。应用层中的服务和命令处理程序执行应用程序逻辑，调用领域层中的领域对象来完成具体的业务处理。应用层还负责事务管理和协调不同领域对象之间的交互。
3. 领域层（Domain Layer）：是整个架构的核心，包含了业务领域的核心概念、业务规则和业务逻辑。领域层由聚合、实体、值对象等领域对象组成，它们封装了业务行为和状态，并通过领域服务之间的交互来完成复杂的业务操作。
4. 基础设施层（Infrastructure Layer）：提供支持上述三个层次的基础设施和技术实现，如数据库访问、消息队列、外部服务和框架等。基础设施层隐藏了底层技术的细节，提供统一的接口供上层调用。

**DDD架构模型**
[![pPvQoKe.png](https://z1.ax1x.com/2023/10/08/pPvQoKe.png)](https://imgse.com/i/pPvQoKe)

# COLA
### COLA架构
[![pPvQv28.png](https://z1.ax1x.com/2023/10/08/pPvQv28.png)](https://imgse.com/i/pPvQv28) [![pPvlSKg.png](https://z1.ax1x.com/2023/10/08/pPvlSKg.png)](https://imgse.com/i/pPvlSKg)
**Adapter层**：负责对前端展示的路由和适配，对应传统的B/S架构来说，adapter相当于之前的controller **App层**：主要负责获取输入，组装上下文，参数校验，调用领域层做业务处理，如果需要的话，发送消息通知等。层次是开放的，应用层也可以绕过领域层，直接访问基础实施层
**Domain层**：主要是封装了核心业务逻辑，并通过领域服务（Domain Service）和领域对象（Domain Entity）的方法对App层提供业务实体和业务逻辑计算。领域是应用的核心，不依赖任何其他层次； **Infrastructure层**：主要负责技术细节问题的处理，比如数据库的CRUD、搜索引擎、文件系统、分布式服务的RPC等。此外，领域防腐的重任也落在这里，外部依赖需要通过gateway的转义处理，才能被上面的App层和Domain层使用。 **Client层**：对外提供的功能API，如果外部模块需要使用本项目功能只需引用 client 中的 API 接口即可实现功能、

| adapter | web | 处理页面请求的Controller |
| --- | --- | --- |
| app | 业务功能包（user、order）放xxxServiceImpl | 业务名 |
| app | executor | 修改相关的执行逻辑 |
| app | query(executor包下) | 查询相关的执行逻辑 |
| client | api(放xxxService) | 存放对外功能的api |
| client | dto（data、query） | 对外返回的对象及调用方传入的参数对象 |
| domain | gateway | 防腐层，让 Infrastructure 层实现逻辑 |
| domain | 业务功能包（entity、值对象） | 根据业务功能分包，包中存放业务实体及值对象 |
| infrastructure | convertor | 存放将 DO 转化为 entity的类 |
| infrastructure | config | 存放配置相关 |
| infrastructure | gateway.impl | 实现 domain 层的 gateway 接口的实现类 |
| infrastructure | dataobject | 存放数据库对象的DO |
| infrastructure | mapper | mapper文件 |

[![pPvlQaR.png](https://z1.ax1x.com/2023/10/08/pPvlQaR.png)](https://imgse.com/i/pPvlQaR)
