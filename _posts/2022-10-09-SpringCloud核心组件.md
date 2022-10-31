---
layout: post
title: SpringCloud核心组件
date: 2022-10-09
author: 少年不年少
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - 学习
    - SpringCloud



---

## Eureka

注册中心，详见[http://stwh.club/2022/09/26/注册中心/](http://stwh.club/2022/09/26/注册中心/)

## Ribbon

负载均衡，运行在消费者端。消费者调用服务之前在内部使用负载均衡算法，决定调用哪个后台服务。

而`nginx`做负载均衡是在接收所有请求后根据配置的负载均衡算法做路由分发。

### Ribbon的负载均衡算法

- **RoundRobinRule**：轮询策略。`Ribbon` 默认采用的策略。若经过一轮轮询没有找到可用的 `provider`，其最多轮询 10 轮。若最终还没有找到，则返回 null。
- **RandomRule**: 随机策略，从所有可用的 provider 中随机选择一个。
- **RetryRule**: 重试策略。先按照 RoundRobinRule 策略获取 provider，若获取失败，则在指定的时限内重试。默认的时限为 500 毫秒。

另外，还可以自定义负载均衡算法，你只需要实现 `IRule` 接口，然后修改配置文件或者自定义 `Java Config` 类即可。

## Open Feign

其实就是封装和简化了`RestTemplate`调用接口的复杂和重复度。

`Open Feign` 也是运行在消费者端，集成`Ribbon`了做负载均衡。

## Hystrix

> Hystrix是一个库，可通过添加等待时间容限和容错逻辑来帮助您控制这些分布式服务之间的交互。Hystrix通过隔离服务之间的访问点，停止服务之间的级联故障并提供后备选项来实现此目的，所有这些都可以提高系统的整体弹性。

使用`Hystrix`的目的主要提高自身系统的容错性，简单说就是在下游系统崩溃时自身不崩溃，避免引起雪崩现象。通常有两种方式：熔断和降级。

### 熔断

当指定时间窗内的请求失败率达到设定阈值时，系统将通过 **断路器** 直接将此请求链路断开。可以使用简单的 `@HystrixCommand` 注解来标注某个方法，这样 `Hystrix` 就会使用 **断路器** 来“包装”这个方法，每当调用时间超过指定时间时(默认为1000ms)，断路器将会中断对这个方法的调用。

### 降级

并不会直接断开，而是“降低服务”或者“服务转移”，通常使用`fallbackMethod`注解来实现，当指定时间窗内的请求失败率达到设定阈值时调用`fallbackMethod`注解配置的方法。

## 微服务网关Zuul

> ZUUL 是从设备和 web 站点到 Netflix 流应用后端的所有请求的前门。作为边界服务应用，ZUUL 是为了实现动态路由、监视、弹性和安全性而构建的。它还具有根据情况将请求路由到多个 Amazon Auto Scaling Groups（亚马逊自动缩放组，亚马逊的一种云计算方式） 的能力。



## 配置中心Config

## Spring Cloud Bus