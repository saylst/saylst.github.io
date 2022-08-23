---
layout: post
title: SpringBoot学习笔记
date: 2021-06-18
author: 少年不年少
header-img: img/post/LBJ.jpg
catalog: true
tags:
    - 学习
    - SpringBoot

---

Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。


# pom依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>myproject</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.1.RELEASE</version>
    <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
	</parent>

	<!-- Additional lines to be added here... -->

</project>
```

# 注解

## @SpringBootApplication

包含@ComponentScan`,`@EnableAutoConfiguration`,`@SpringBootConfiguration三个注解。

###  @EnableAutoConfiguration注释

这个注释告诉SpringBoot根据你添加的jar依赖关系“猜测”你想要如何配置Spring。由于`spring-boot-starter-web`添加了Tomcat和Spring MVC，因此自动配置假定您正在开发Web应用程序并相应地设置Spring。

### @ComponentScan

使用过spring框架的小伙伴都知道，spring里有四大注解：`@Service`,`@Repository`,`@Component`,`@Controller`用来定义一个bean.`@ComponentScan`注解就是用来自动扫描被这些注解标识的类，最终生成ioc容器里的bean．

### @SpringBootConfiguration

这个注解的作用与`@Configuration`作用相同，都是用来声明当前类是一个配置类．可以通过`＠Bean`注解生成IOC容器管理的bean.

# 可执行Jar包

可执行jar（有时称为“fat jar”）是包含已编译类以及代码需要运行的所有jar依赖项的归档。

要创建可执行jar，我们需要将`spring-boot-maven-plugin`添加到`pom.xml`。为此，请在`dependencies`部分下方插入以下行：

```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```

# Starters

Starters是一组方便的依赖描述符，您可以在应用程序中包含这些描述符。您可以获得所需的所有Spring和相关技术的一站式服务，而无需搜索示例代码和复制粘贴依赖描述符的负载。常用的starters如下：

| 名称                           | 描述                                            |
| ------------------------------ | ----------------------------------------------- |
| `spring-boot-starter`          | 核心启动器，包括自动配置支持，日志记录和YAML    |
| `spring-boot-starter-activemq` | 使用Apache ActiveMQ进行JMS消息传递的入门者      |
| `spring-boot-starter-amqp`     | 使用Spring AMQP和Rabbit MQ的入门者              |
| `spring-boot-starter-aop`      | 使用Spring AOP和AspectJ进行面向方面编程的入门者 |
| `spring-boot-starter-artemis`  | 使用Apache Artemis进行JMS消息传递的入门者       |
| `spring-boot-starter-batch`    | 使用Spring批处理的初学者                        |
| `spring-boot-starter-cache`    | 使用Spring Framework的缓存支持的初学者          |