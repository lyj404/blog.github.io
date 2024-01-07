---
title: SpringBoot自动装配原理
date: 2023-09-26 21:42:12
tags: SpringBoot
categories: Java
keywords: SpringBoot自动装配原理
cover: https://s11.ax1x.com/2023/09/26/pPHNSxg.png
description: Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。
---
# SPI
`SpringBoot`的自动装配是通过SPI的方式实现的，并进行了进一步的优化，从而实现了自动装配。`SPI`全称为Serveice Provider Interface，是Java提供的一种服务发现机制。它**允许不同的组件在运行时动态的扩展、替换和加载实现**。`SPI`是一种基于接口和实现分离的设计模式。
在`SPI`机制中，定义一个接口作为服务的标准化接口，然后通过类路径下提供特定配置文件来指定具体的实现。这个配置文件通常位于`META-INF/services`目录下，以接口的全限定名命名。配置文件中列出了实现该接口的具体类的全限定名。
当需要使用某项服务时，应用程序可以通过`SPI`机制查找平加载对应的实现类。Java运行时会通过读取配置文件获取到实现类的信息，并实例化对应的类。这样能够做到在不修改代码的情况下，通过添加/替换配置文件中的实现类，来改变程序的行为或者增加新功能。

## SPI的应用
1. **JDBC数据库驱动**
2. **日志框架（SLF4）**

# SpringBoot自动装配原理
SpringBoot自动配置是默认开启的，`spring.boot.enableautoconfiguration=true`，可以通过`application.properties`或`application.yml`来关闭自动配置。
`SpringBoot`应用都会创建一个启动类，启动类上包含了`@SpringBootApplication`注解，这个注解是一个复合的注解，分别对这三个注解进行了封装`@SpringBootConfiguration`、`@EnableAutoConfiguration`、`@ComponentScan`。
```java
@SpringBootApplication  
public class DemoApplication {  
  
    public static void main(String[] args) {  
        SpringApplication.run(DemoApplication.class, args);  
    }  
  
}
```
**`@SpringBootApplication`注解的实现**
```java
@Target({ElementType.TYPE})  
@Retention(RetentionPolicy.RUNTIME)  
@Documented  
@Inherited  
@SpringBootConfiguration  
@EnableAutoConfiguration  
@ComponentScan(  
    excludeFilters = {@Filter(  
    type = FilterType.CUSTOM,  
    classes = {TypeExcludeFilter.class}  
), @Filter(  
    type = FilterType.CUSTOM,  
    classes = {AutoConfigurationExcludeFilter.class}  
)}  
)  
public @interface SpringBootApplication
```

`@SpringBootConfiguration`：用于标识一个类是SpringBoot的配置类。
`@EnableAutoConfiguration`：启动SpringBoot的自动配置机制。
`@ComponentScan`：扫描被`@Component`注解的Bean，注解默认会扫描启动类所在的包下的所有类。

`@EnableAutoConfiguration`是实现SpringBoot自动配置的核心，该注解上通过`@Import注解`导入了一个`AutoConfigurationImportSelector`类，`AutoConfigurationImportSelector` 类实现了 `ImportSelector`接口，也就实现了这个接口中的 `selectImports`方法，这个方法主要是用来**获取所有符合条件的类的全限定类名。**
`@EnableAutoConfiguration`源码
```java
@Target({ElementType.TYPE})  
@Retention(RetentionPolicy.RUNTIME)  
@Documented  
@Inherited  
@AutoConfigurationPackage  
@Import({AutoConfigurationImportSelector.class})  
public @interface EnableAutoConfiguration {  
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";  
  
    Class<?>[] exclude() default {};  
  
    String[] excludeName() default {};  
}
```
`AutoConfigurationImportSelector`源码
```java
public class AutoConfigurationImportSelector implements DeferredImportSelector{  
  
    public String[] selectImports(AnnotationMetadata annotationMetadata) {  
        if (!this.isEnabled(annotationMetadata)) {  
            return NO_IMPORTS;  
        } else {  
            AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry(annotationMetadata);  
            return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());  
        }  
    }
}    
```
通过`selectImports()`方法调用`getAutoConfigurationEntry(annotationMetadata)`，`getAutoConfigurationEntry(annotationMetadata)`方法通过`SpringFactoriesLoader.loadFactoryNames()`，扫描所有含有`META-INF/spring.factories`的`jar`包，然后读取`META-INF/spring.factories`文件中所配置的类的全限定类名。在这些配置类中所定义的Bean会**根据条件注解来决定**是否需要将其导入到Spring的容器中。
一般条件判断都会有`@ConditionalOnClass`这样的注解，判断是否有对应的class文件，如果有则加载该类，并将这个配置类所有的Bean放入Spring容器中。
