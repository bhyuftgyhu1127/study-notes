# Spring面试题

## 1、Spring的bane的作用域

- singleton：单例模式，默认是singleton

- prototype：每次容器调用bean时都会创建一个新的对象

- request：每次http请求都会创建一个对象

- session：同一个session共享一个对象

- globalsession：每个全局的HTTP Session，使用session定义的Bean都将产生一个新实例。典型情况下，仅在使用portlet context的时候有效。同样只有在Web应用中使用Spring时，该作用域才有效

##  2、Spring的bean是线程安全的吗？

构成线程不安全的三个要素：

1. 多线程环境
2. 访问同一个资源
3. 资源具有状态性

Spring的bean是单例，处于一个多线程环境。

关键看第三点：我们的bean是无状态的，所以从这个点来说是线程安全的。

无状态：就是没有存储数据 ，就是没有通过状态判断数据依据。 

## 3、Spring支持的事务传播特性

1.PROPAGATION_REQUIRED(默认实现)：当前没有事务则新建事务，有则加入当前事务

2.PROPAGATION_SUPPORTS：支持当前事务，如果当前没有事务则以非事务方式执行

3PROPAGATION_MANDATORY：使用当前事务，如果没有则抛出异常

4PROPAGATION__REQUIRES_NEW：新建事务，如果当前有事务则把当前事务挂起

5PROPAGATION_NOT_SUPPORIED：以非事务的方式执行，如果当前有事务则把当前事务挂起

6PROPAGATION_NEVER：以非事务的方式执行，如果当前有事务则抛出异常

7PROPAGATION_NESTED：如果当前存在事务，则在嵌套事务内执行，如果当前没有事务，则执行1



## 4、Spring的IoC理解

1. IOC就是控制反转，指创建对象的控制权移交给spring框架进行管理，并由spring根据配置文件去创建实例和管理各个实例之间的依赖关系。
2. 以前的控制权自己把控，ioc让对象不用去new了，由spring自动生产，使用java的反射机制，根据配置文件在运行时动态创建
3. spring的ioc有三种方式：构造器注入、setter注入、注解注入



## 5、Spring的AOP理解

AOP，面向切面编程，用于将那些与业务无关的事情抽取出来，让我们只需要关系业务的实现。

AOP底层使用了：**动态代理**，静态代理的代表为**AspectJ**，动态代理代表spring aop

Spring AOP中的动态代理主要有两种方式，JDK动态代理和CGLib动态代理：

1. JDK动态代理只提供接口，实现InvocationHandler接口和Proxy类

**IoC让相互协作的组件保持松散的耦合，而AOP编程允许你把遍布于应用各层的功能分离出来形成可重用的功能组件。**



## Spring Bean的生命周期？

1. 解析类得到BeanDefinition(弟非呢性)

2. 如果有多个构造方法，则要推断构造方法

3. 确定好构造方法，进行实例化得到一个对象

4. 对对象加入@Autowired注解的属性进行属性填充

5. 回调Aware方法，如BeanNameaware，BeanFactoryAware

6. 调用BeanPostProcessor（泼瑟瑟）的初始前方法

7. 调用初始化方法（InitializingBean）

8. 调用BeanPostProcessor的初始后方法，这边就是aop

9. 如果创建的bean是单例，会放入单例线程池

10. 使用bean

11. spring容器关闭，销毁容器。

    





























