---
title: Spring循环依赖
date: 2023-09-07 18:01:30
tags: Spring
categories: Java
keywords: Spring循环依赖
cover: https://z1.ax1x.com/2023/09/23/pPT3ooF.jpg
description: 循环依赖是指两个或多个模块或组件之间相互依赖，形成一个闭环情况。而Spring通过三级缓存解决循环依赖。
---
# 循环依赖
循环依赖是指两个或多个模块或组件之间相互依赖，形成一个闭环情况。
```java
@Component
public class A { 
	@Autowired 
	private B b;
}
@Component
public class B { 
	@Autowired 
	private A a;
}
```
[![循环依赖](https://z1.ax1x.com/2023/09/16/pPfYDFs.png)](https://imgse.com/i/pPfYDFs)
# Spring解决循环依赖的限制条件
1. 出现循环依赖的Bean必须是单例Bean，原型Bean不可以
	原型Bean每次获取的时候都会创建一个新的Bean，假设A、B循环依赖了，A里面要注入B，B里面要注入A，注入一次就会产生一个新的Bean，最终会无穷无尽。
2. 不全是使用构造器注入的
	* 均采用setter方法注入的，可以被Spring解决
	* 均采用构造器方式注入的，不可以被Spring解决
	* setter和构造器两种方式都使用，需要具体分析
	
>使用构造器注入时，当A依赖B，而B又依赖A，Spring会尝试先创建A。但是，在创建A时，由于它依赖的B还没有创建完成，无法传递给构造器进行注入，从而导致循环依赖无法被解决。
# 三级缓存
Spring通过三级缓存来解决循环依赖问题。
1. 单例对象缓存(singletonObjects)：存放完整的Bean对象，包括所有属性都已经注入完成的Bean。
2. 早期对象缓存(earlySingletonObjects)：存放半成品对象，即Bean的实例化和依赖注入已经完成，但是初始化还未执行完成的Bean。
3. 单例工厂缓存(singletonFactories)：存放用于创建完整Bean对象的Factory对象。
## Spring 解决循环依赖的过程
1. Spring容器启动之后，开始创建Bean。
2. 容器根据配置信息**实例化完成A，但是还没有初始化**，紧接着A与一个ObjectFactory对象放入三级缓存。**如果A被AOP代理，通过这个工厂获取到的就是A代理后的对象，如果没有代理，工厂最后获取到的就是A的实例化对象**
3. 初始化A，为A属性赋值，发现要依赖注入B，于是将B实例化，在B还未初始化的时候将B和一个ObjectFactory对象放入三级缓存
4. 初始化B，发现需要依赖注入A，此时在三级缓存中找到了A与`ObjectFactory<?> singletonFactory`，通过`singletonFactory.getObject()`，得到A的引用，并将其存入二级缓存，且从三级缓存移除。
5. B从二级缓存中获取到不完整的A，并注入到B中，B初始化完成，**并将B在二级缓存、三级缓存中的引用清除，同时将实例化完成的B存入一级缓存**
6. B实例化完成后，继续A的初始化，A从一级缓存中获取到B，同样，**并将A二级缓存、三级缓存中的引用清除，同时将实例化完成的A存入一级缓存**
7. A和B实例完成，并且二级缓存和三级缓存中都没有A和B了
[![Spring解决循环依赖](https://z1.ax1x.com/2023/09/16/pPfYrYn.png)](https://imgse.com/i/pPfYrYn)
# 为什么要三级缓存？二级为什么不可以？
三级缓存中存放的是生成具体对象的匿名内部类，在获取对象的时候，既可以生成代理对象，又可以生成普通的对象，使用三级缓存可以保证不管什么时候使用的都是一个对象。

如果只有二级缓存的情况，往二级缓存中放一个普通Bean对象，在Bean初始化的时候，通过`BeanPostProcessor`生成代理对象之后，会覆盖掉二级缓存中普通Bean对象，从而导致获取的Bean对象不一致。