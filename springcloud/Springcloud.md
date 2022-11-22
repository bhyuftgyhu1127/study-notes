# Springcloud是什么？

SpringCloud=分布式微服务架构的一站式解决方案，是多种微服务架构落地技术的集合体，俗称微服务全家桶。



## SpringCloud有多少种技术？



<img src="C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210307214714670.png" alt="image-20210307214714670" style="zoom:70%;" />.

## 学习目标

<img src="C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210307215444285.png" alt="image-20210307215444285" style="zoom:67%;" />.



# 1. 概述

spring cloud Gateway是由webFlux+Netty+Reactor实现的**响应式**的网关。

Spring Cloud Gateway 旨在为微服务架构提供一种简单且有效的 API 路由的管理方式，并基于 Filter 的方式提供网关的基本功能，例如说安全认证、监控、限流等等。

Spring Cloud Gateway 定位于取代 Netflix [Zuul](https://github.com/Netflix/zuul)，成为 Spring Cloud 生态系统的新一代网关。目前看下来非常成功，老的项目的网关逐步从 Zuul 迁移到 Spring Cloud Gateway，新项目的网关直接采用 Spring Cloud Gateway。相比 Zuul 来说，Spring Cloud Gateway 提供**更优秀的性能，更强大的有功能**。

#### Spring Cloud Gateway 的特征如下：

FROM [spring-cloud-gateway#features](https://github.com/spring-cloud/spring-cloud-gateway#features) 翻译

- 基于 Java 8 编码
- 基于 Spring Framework 5 + Project Reactor + Spring Boot 2.0 构建
- 支持动态路由，能够匹配任何请求属性上的路由
- 支持内置到 Spring Handler 映射中的路由匹配
- 支持基于 HTTP 请求的路由匹配（Path、Method、Header、Host 等等）
- 集成了 Hystrix 断路器
- 过滤器作用于匹配的路由
- 过滤器可以修改 HTTP 请求和 HTTP 响应（增加/修改 Header、增加/修改请求参数、改写请求 Path 等等）
- 支持 Spring Cloud DiscoveryClient 配置路由，与服务发现与注册配合使用
- 支持限流

特性有点多，一边入门一边了解。实际上 Spring Cloud Gateway 还提供了很多其它特性，例如说还能够作为 WebSocket 的网关。

# 2. 为什么使用网关？

- [《一文带你 API 网关从入门到放弃》](D:\学习笔记\springcloud\一文带你 API 网关从入门到放弃.md)



# Eureka----(服务注册)

### 背景：

​	Eureka是Netflix开源的一款提供服务注册和发现的产品，它提供了完整的Service Registry和Service Discovery实现。也是springcloud体系中最重要最核心的组件之一。

​	RPC远程调用框架的核心设计思想：在于注册中心，因为注册中心是管理每个服务与服务之间的一种依赖关系（服务治理的概念）。

​	**什么是服务治理？**

![](https://img2018.cnblogs.com/blog/1556518/201902/1556518-20190227203010848-1946180761.png).

　**服务中心：**

![](https://img2018.cnblogs.com/blog/1556518/201902/1556518-20190227204857235-2043055738.png)

服务中心又称注册中心，管理各种服务功能包括服务的注册、发现、熔断、负载、降级等，比如dubbo admin后台的各种功能。有了服务中心调用关系会有什么变化，画几个简图来帮忙理解：



1、项目A调用项目B

正常调用项目A请求项目B：

![](https://img2018.cnblogs.com/blog/1556518/201902/1556518-20190213140426595-558418896.png)。

有了服务中心之后，任何一个服务都不能直接去掉用，都需要通过服务中心来调用：

![](https://img2018.cnblogs.com/blog/1556518/201902/1556518-20190213140511810-27550196.png).



　2、项目A调用项目B，项目B在调用项目C

![.](https://img2018.cnblogs.com/blog/1556518/201902/1556518-20190213140550836-1491170164.png).

这时候调用的步骤就会为两步：

第一步：项目A首先从服务中心请求项目B服务器，

第二步：项目B在从服务中心请求项目C服务。

![](https://img2018.cnblogs.com/blog/1556518/201902/1556518-20190213140628808-251586218.png).

上面的项目只是两三个相互之间的简单调用，但是如果项目超过20个30个呢，在15年底的时候我司分布式的项目就达到了二十几个，画一张图来描述几十个项目之间的相互调用关系全是线条，任何其中的一个项目改动，就会牵连好几个项目跟着重启，巨麻烦而且容易出错。通过服务中心来获取服务你不需要关注你调用的项目IP地址，由几台服务器组成，每次直接去服务中心获取可以使用的服务去调用既可。

　　由于各种服务都注册到了服务中心，就有了去做很多高级功能条件。比如几台服务提供相同服务来做均衡负载；监控服务器调用成功率来做熔断，移除服务列表中的故障点；监控服务调用时间来对不同的服务器设置不同的权重等等。

 

**一、Eureka简介**

Spring Cloud 封装了 Netflix 公司开发的 Eureka 模块来实现服务注册和发现。Eureka 采用了 C-S 的设计架构。Eureka Server 作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用 Eureka 的客户端连接到 Eureka Server，并维持心跳连接。这样系统的维护人员就可以通过 Eureka Server 来监控系统中各个微服务是否正常运行。Spring Cloud 的一些其他模块（比如Zuul）就可以通过 Eureka Server 来发现系统中的其他微服务，并执行相关的逻辑。

Eureka由两个组件组成：Eureka服务器和Eureka客户端。Eureka服务器用作服务注册服务器。Eureka客户端是一个java客户端，用来简化与服务器的交互、作为轮询负载均衡器，并提供服务的故障切换支持。Netflix在其生产环境中使用的是另外的客户端，它提供基于流量、资源利用率以及出错状态的加权负载均衡。

![](https://img2018.cnblogs.com/blog/1556518/201902/1556518-20190213141126392-1901419041.png).

上图简要描述了Eureka的基本架构，由3个角色组成：

1、Eureka Server

- 提供服务注册和发现

2、Service Provider

- 服务提供方
- 将自身服务注册到Eureka，从而使服务消费方能够找到

3、Service Consumer

- 服务消费方
- 从Eureka获取注册服务列表，从而能够消费服务

Spring Cloud官网的定义比较抽象，我们可以从简单的东西开始。Spring Cloud是基于Spring Boot的， 最适合用于管理Spring Boot创建的各个微服务应用。要管理分布式环境下的各个Spring Boot微服务，必然存在服务的注册问题。所以我们先从服务的注册谈起。既然是注册，必然有个管理注册中心的服务器，各个在Spring Cloud管理下的Spring Boot应用就是需要注册的client。

　Spring Cloud使用erureka server,  然后所有需要访问配置文件的应用都作为一个erureka client注册上去。eureka是一个高可用的组件，它没有后端缓存，每一个实例注册之后需要向注册中心发送心跳，在默认情况下erureka server也是一个eureka client ,必须要指定一个 server。



### 二、实例展示

项目架构：

![](https://img2018.cnblogs.com/blog/1556518/201902/1556518-20190213141719709-899169357.png).

![](https://img2018.cnblogs.com/blog/1556518/201902/1556518-20190213141621570-188654095.png).

