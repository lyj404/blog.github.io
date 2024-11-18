---
title: SpringBoot使用HTTPS
date: 2024-11-18 20:15:29
tags: Spring Boot
categories: Java
keywords: SpringBoot使用HTTPS
cover: https://s11.ax1x.com/2023/09/26/pPHNSxg.png
description: 将Spring Boot项目的HTTP转化成HTTPS
---
将SpringBoot项目的HTTP转化成HTTPS只需要在`application.yml`进行相关的配置，或者是通过实现 `WebServerFactoryCustomizer<ConfigurableWebServerFactory>` 创建一个配置类，以自动将 HTTP 流量重定向到 HTTPS。这两个方法均可实现将HTTP转为HTTPS。
**具体步骤如下：**
# 获取SSL证书
可在阿里云申请免费的SSL证书，也可以使用Java 的 `keytool` 工具生成自签名证书，由于嫌麻烦所以我使用的是Java 的 `keytool` 工具生成自签名证书。
```shell
keytool -genkey -alias myalias -keyalg RSA -keystore keystore.p12 -storetype PKCS12
```
> 该命令执行之后，会在命令行提示你输入一些密码以及一些其他信息，如姓名和组织。

# 方法一
**通过配置文件配置HTTPS**
在 `application.yml`或`application.properties` 文件中，指定密钥库路径、类型、密码和别名：
**yaml文件写法：**
```yaml
server:
  ssl:
    key-store: classpath:keystore.p12
    key-store-password: your_password
    key-store-type: PKCS12
    key-alias: myalias
  port: 8443
```
**properties文件写法：**
```properties
server.port=8443
server.ssl.key-store=classpath:keystore.p12
server.ssl.key-store-password=your_password
server.ssl.key-store-type=PKCS12
server.ssl.key-alias=myalias
```
# 方法二
**使用WebServerFactoryCustomizer类配置HTTPS**
创建一个配置类实现`WebServerFactoryCustomizer`并重写`customize()`方法，具体代码如下：
```java
@Component  
public class ServerConfig implements WebServerFactoryCustomizer<ConfigurableWebServerFactory> {  
  
    @Override  
    public void customize(ConfigurableWebServerFactory factory) {  
        Ssl ssl = new Ssl();  
        // 启动ssl  
        ssl.setEnabled(true);  
        ssl.setKeyStore("classpath:keystore.p12");  
        ssl.setKeyStorePassword("your_password");  
        ssl.setKeyStoreType("PKCS12");  
        ssl.setKeyAlias("myalias");  
        factory.setSsl(ssl);  
        factory.setPort(8443);  
    }  
}
```

>SSL证书需放在项目的`src\main\resources`下，且方法一和方法二只需实现一种即可。

**如果将`key-store-password`错误的写成`key-password`，则会报错如下：**
```text
Caused by: org.apache.catalina.LifecycleException: Protocol handler start failed  
at org.apache.catalina.connector.Connector.startInternal(Connector.java:1061) ~[tomcat-embed-core-10.1.30.jar:10.1.30]  
at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:164) ~[tomcat-embed-core-10.1.30.jar:10.1.30]  
at org.apache.catalina.core.StandardService.addConnector(StandardService.java:219) ~[tomcat-embed-core-10.1.30.jar:10.1.30]  
... 17 common frames omitted  
Caused by: java.lang.IllegalArgumentException: Private key must be accompanied by certificate chain  
```
