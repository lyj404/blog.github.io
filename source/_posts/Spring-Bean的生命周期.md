---
title: Spring Bean的生命周期
date: 2023-09-07 16:06:24
tags: Spring
categories: Java
keywords: Spring Bean的生命周期
cover: https://z1.ax1x.com/2023/09/23/pPT3ooF.jpg
description: 在Java应用中，普通的Java Bean的生命周期很简单。而Spring的Bean的生命周期则比较复杂，Spring的Bean由IOC容器进行实例化、组装和管理。
---
# Bean的生命周期
在Java应用中，普通的Java Bean的生命周期很简单。使用关键字`new`对Bean进行实例化，之后Bean就可使用了，当Bean不在被使用就会被Java的垃圾回收机制回收。概括的说就是两个阶段：
1. 实例化
2. 不使用之后垃圾回收

Spring的Bean的生命周期则比较复杂，Spring的Bean由`IOC`容器进行实例化、组装和管理。`IOC(Inversion of Control，控制反转)`是一种软件设计原则，用于实现松耦合和可维护的代码结构。
>Spring Bean的生命周期主要分为四个部分
1. 实例化(Instantiation)
2. 属性赋值(Populate)
3. 初始化(Initialization)
4. 销毁(Destruction)
[![生命周期四阶段](https://z1.ax1x.com/2023/09/16/pPfaWg1.png)](https://imgse.com/i/pPfaWg1)
**Spring Bean详细生命周期：**
[![Spring-Bean生命周期](https://z1.ax1x.com/2023/09/16/pPfafjx.png)](https://imgse.com/i/pPfafjx)

在Spring框架中，Bean的生命周期经历了以下几个阶段：
1. 实例化(Instantiation)：Spring容器根据配置信息或注解创建Bean的实例。
2. 属性赋值(Populate)：Spring容器将属性值和依赖注入到Bean中。
3. 设置Bean名称：如果Bean实现了`BeanNameAware`接口，将会调用`setBeanName()`方法，把Bean的名称传递给它。
4. 设置BeanFactory的引用：如果Bean实现了`BeanFactoryAware`接口，将会调用`setBeanFactory()`方法，把Bean所在的BeanFactory传递给它。
5. 设置其他自定义上下文：如果Bean实现了`ApplicationContextAware`接口，将会调用`setApplicationContext()`方法，把ApplicationContext传递给它。
6. Bean后处理(BeanPostProcessor)：Spring容器会对所有的BeanPostProcessor进行初始化，这些后处理可以对Bean实例化前后进行一些自定义操作。
7. 初始化(Initialization)：如果Bean实现了`InitializingBean`接口，将会调用`afterPropertiesSet()`方法来执行自定义初始化逻辑。另外，还可以通过配置文件使用`init-method`属性制定Bean的初始化方法。
8. 自定义初始化(Custom Initialization)：在Bean初始化之后，可以执行一些自定义的初始化逻辑。
9. 销毁(Destruction)：如果Bean实现了`DisposableBean`接口，将会调用`destroy()`方法来执行自定义销毁逻辑。同样，还可以通过配置文件使用`destory-method`属性制定Bean的初始化方法。
10. 自定义销毁(Custom Destruction)：在 Bean 销毁之前，可以执行一些自定义的销毁逻辑。
>Bean的生命周期可以通过配置方式进行管理，例如使用XML配置或者是注解。同时，Spring还提供了各种扩展点和回调接口，使开发者能够在Bean的生命周期的不同阶段进行定制化操作。
# 扩展点
Spring如果监测到Bean实现了`Aware`接口，则会为其注入相应的依赖。**因此通过让Bean实现`Aware`接口，能够让Bean获得相应的Spring容器资源**
**`Aware`接口：**
1. BeanNameAware：注入当前Bean对应的名称
2. BeanClassLoaderAware：注入加载当前Bean的ClassLoader
3. BeanFactoryAware：注入当前BeanFactory的引用
## BeanPostProcessor
`BeanPostProcessor`是Spring的一个重要的接口，用于在Bean初始化前后进行扩展和自定义处理。它是Bean生命周期的一部分，主要用于在容器实例化Bean和将其添加到应用程序上下文之后，以及Bean初始化前后对Bean的定制化操作。
```java
public interface BeanPostProcessor {
	default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}

	default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}
}
```
1. postProcessBeforeInitialization(Object bean, String beanName)：Bean初始化之前调用该方法。接收两个参数：正在初始化的Bean的对象和Bean的名称。可以对此方法中的Bean进行修改和增强。如果返回的对象不为null，则该对象作为最终的Bean实例。
2. postProcessAfterInitialization(Object bean, String beanName)：在Bean初始化之后调用该方法。接收两个参数：已经完成初始化的Bean对象和Bean的名称。可以对此方法中的Bean进行修改和增强。如果返回的对象不为null，则该对象作为最终的Bean实例。

通过实现`BeanPostProcessor`接口，在Spring容器实例化Bean和初始化Bean的过程中介入，可以实现一些功能：
* 属性值的校验、修正和增强。
* 动态代理，用于实现AOP的切面
* 注入依赖关系的解析和处理
* 条件化的Bean的创建和初始化
* 对Bean进行包装或装饰
