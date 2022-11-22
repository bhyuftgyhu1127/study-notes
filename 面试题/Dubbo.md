## Dubbo面试题

## 1、dubbo都支持什么协议，推荐用哪种？

- dubbo://(推荐)
- rmi://
- hessian://
- http://
- webservice://
- thrift://
- memcached://
- redis://
- rest://

## 2、dubbo里面有几种节点角色？

| 节点      | 角色说明                               |
| --------- | -------------------------------------- |
| Provider  | 暴露服务的服务提供方                   |
| Consumer  | 调用远程服务的服务消费方               |
| Registry  | 服务注册于发现的注册中心               |
| Monitor   | 统计服务的调用次数和调用时间的监控中间 |
| Container | 服务运行容器                           |

## 3、Dubbo默认使用什么注册中心，还有别的选择吗？

推荐使用 Zookeeper 作为注册中心，还有 Redis、Multicast、Simple 注册中心，但不推荐。



## 4、**Dubbo推荐使用什么序列化框架，你知道的还有哪些？**

推荐使用`Hessian`序列化，还有Duddo、FastJson、Java自带序列化。



## 5、Dubbo默认使用的是什么通信框架，还有别的选择吗？

Dubbo默认使用`Netty`框架



## 6、Dubbo有哪几种负载均衡策略，默认是哪种？

| 负载均衡策略               | 说明                                         |
| :------------------------- | :------------------------------------------- |
| Random LoadBalance         | 随机，按权重设置随机概率（默认）             |
| RoundRobin LoadBalance     | 轮询，按公约后的权重设置轮询比率             |
| LeastActive LoadBalance    | 最少活跃调用数，相同活跃的随机               |
| ConsistentHash LoadBalance | 一致性Hash，相同参数的请求总是发到同一提供者 |


		
	
	













