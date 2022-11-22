# SpringBoot



SpringBoot来简化Spring应用开发，约定大于配置，去繁化简



## 1、Springboot的核心注解是哪个？它由那几个注解组成？

启动类上面的注解是@SpringBootApplication，它是springBoot的核心注解，主要组合包含以下3个：

- @SpringBootConfiguration：组合了@Configuration注解，实现配置文件功能。
- @EnableAutoConfiguration：打开自动配置的功能，可以关闭某个自动配置的功能，如关闭数据库自动配置功能
  - `@SpringBootApplication（exclued = {DataSourceAutoConfiguration.class}）`
- @ComponentScan:spring组件扫描



## 2、SpringBoot 实现热部署有哪几种方式？

- spring loaded
- spring-boot-devtools



## 3、springboot支持的默认日志框架是什么

- spring-boot: 默认采用Logback作为日志框架.



## 4、SpringBoot自动配置的实现原理（必需问）

springboot的核心就是:`自动配置`,自动配置又是基于条件判断来配置Bean。关于自动配置的源码在spring-boot-autoconfigure-2.0.3.RELEASE.jar

![在这里插入图片描述](https://img-blog.csdn.net/20180625161728593?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F4ZWxhMzBX/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70).

### 解析

SpringBoot运行原理

先看@SpringBootApplication

springboot的banb：`2.2.12.RELEASE`

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
    ...
}
```

主要关注的几个注解：

1. @SpringBootConfiguration：标记当前类是配置类

2. @EnableAutoConfiguration：开启自动配置

3. @ComponentScan：扫描主类所在的同级包以及下级包里的Bean

   

关键是`@EnableAutoConfiguration`

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {

	/**
	 * 可用于在自动配置时重写的环境属性
	 */
	String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

	/**
	 * 排除特定的自动配置类，这样它们就永远不会被应用。
	 */
	Class<?>[] exclude() default {};

	/**
	 * 排除特定的自动配置类名，这样它们就永远不会是
	 * @return 要排除的类名
	 * @since 1.3.0
	 */
	String[] excludeName() default {};

}
```

最关键是@Import(AutoConfigurationImportSelector.class)，借助了**AutoConfigurationImportSelector**，

@EnableAutoConfiguration可以帮助springboot应用将所有符合条件的@Configuration配置都加载到当前springboot创建并使用ioc容器：通过@Import(AutoConfigurationImportSelector.class)导入的配置功能，

AutoConfigurationImportSelector中的方法getCandidateConfigurations，得到待配置的class的类名集合，这个集合就是所有需要进行自动配置的类，而是是否配置的关键在于META-INF/spring.factories文件中是否存在该配置信息

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
				getBeanClassLoader());
		Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
				+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
	}
```

打开，如下图可以看到所有需要配置的类全路径都在文件中，每行一个配置，多个类名逗号分隔,而\表示忽略换行

![image-20210303121222176](C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210303121222176.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201113165102963.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqY2phdmE=,size_16,color_FFFFFF,t_70#pic_center)



### 整个流程如上图所示

@ConditionalOnClass(xxxxx.class):当xxxx在类路径的条件下，当spring.http.encoding=enabled的情况下，如果没有设置则默认为true，即条件符合





样例讲解
以SpringApplicationAdminJmxAutoConfiguration类来看其主要构成部分

@Configuration
@AutoConfigureAfter({JmxAutoConfiguration.class}) //配置完JmxAutoConfiguration后再配置当前类型
// spring.application.admin为前缀,属性为enabled,有值时为true,没有匹配到则为false：以上条件为true则实例化,否则不是实例化
@ConditionalOnProperty( prefix = "spring.application.admin", value = {"enabled"}, havingValue = "true",  matchIfMissing = false)
public class SpringApplicationAdminJmxAutoConfiguration 


都能看到各种各样的条件判断注解，满足条件时就加载这个Bean并实例化
此类的条件注解是：@ConditionalOnProperty

@ConditionalOnBean：当容器里有指定Bean的条件下
@ConditionalOnClass：当类路径下有指定的类的条件下
@ConditionalOnExpression：基于SpEL表达式为true的时候作为判断条件才去实例化
@ConditionalOnJava：基于JVM版本作为判断条件
@ConditionalOnJndi：在JNDI存在的条件下查找指定的位置
@ConditionalOnMissingBean：当容器里没有指定Bean的情况下
@ConditionalOnMissingClass：当容器里没有指定类的情况下
@ConditionalOnWebApplication：当前项目时Web项目的条件下
@ConditionalOnNotWebApplication：当前项目不是Web项目的条件下
@ConditionalOnProperty：指定的属性是否有指定的值
@ConditionalOnResource：类路径是否有指定的值
@ConditionalOnOnSingleCandidate：当指定Bean在容器中只有一个，或者有多个但是指定首选的Bean
这些注解都组合了@Conditional注解，只是使用了不同的条件组合最后为true时才会去实例化需要实例化的类，否则忽略
这种spring4.X带来的动态组合很容易后期配置，从而避免了硬编码，使配置信息更加灵活多变，同时也避免了不必要的意外异常报错。使用的人只要知道配置的条件即可也不用去阅读源码，方便快捷，这也是sprignboot快捷方式带来的好处

参考HttpEncodingAutoConfiguration配置信息如下

@Configuration
@EnableConfigurationProperties(HttpEncodingProperties.class)
@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)
@ConditionalOnClass(CharacterEncodingFilter.class)
@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)
public class HttpEncodingAutoConfiguration {

@Configuration：标明为配置类
@EnableConfigurationProperties(HttpEncodingProperties.class)声明开启属性注入
@ConditionalOnClass(CharacterEncodingFilter.class)当CharacterEncodingFilter在类路径的条件下
@ConditionalOnProperty(prefix = “spring.http.encoding”, value = “enabled”, matchIfMissing = true)当spring.http.encoding=enabled的情况下，如果没有设置则默认为true，即条件符合
@ConditionalOnMissingBean当容器中没有这个Bean时新建Bean

案例扩展


/**
 * @author wuweifeng wrote on 2017/11/25.
 * 根据部署环境动态决定是否启用eureka
 线上的环境开启eureka，就在application-prod.yml里配上open.eureka=true，
 其他的yml什么也不写就行了。这样本地启动时就相当于没有开启EnableDiscoveryClient
 */
@Component
@ConditionalOnProperty(value = "open.eureka")
@EnableDiscoveryClient
public class JudgeEnableDiscoveryClient 

自己实现一个自己的自动配置
项目

xm-common:普通jar项目
- src/main
    java
        BambooServer.java  需要被实例化的服务类
        BambooServerProperties.java 配置信息属性类
        BmbooServiceAutoConfiguration.java 自动配置类
    resources
        META-INF/spring.factories 配置自动配置的属性文件
demo:普通springboot-web项目

需要实例化的服务类

public class BambooServer {
    private String name;

    public String sayServerName(){
        return "I'm " + name + "! ";
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }

}

配置信息对应的属性映射类,需要pom中加入spring-boot-starter依赖

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties(prefix = "bamboo")
public class BambooServerProperties {

    private static final String NAME = "bamboo_server0";
    
    private String name = NAME;
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
}

自动配置文件

/**
 * Author: bamboo
 * Time: 2018/11/25/025
 * Describe: 自动配置类
 * 根据条件判断是否要自动配置，创建Bean
 */
@Configuration
@EnableConfigurationProperties(BambooServerProperties.class)
@ConditionalOnClass(BambooServer.class)//判断BambooServer这个类在类路径中是否存在
@ConditionalOnProperty(prefix = "bamboo",value = "enabled",matchIfMissing = true)
public class BmbooServiceAutoConfiguration {

    @Autowired
    private BambooServerProperties mistraServiceProperties;

    @Bean(name = "bambooServer")
    @ConditionalOnMissingBean(BambooServer.class)//当容器中没有这个Bean时(BambooServer)就自动配置这个Bean，Bean的参数来自于BambooServerProperties
    public BambooServer mistraService(){
        BambooServer mistraService = new BambooServer();
        mistraService.setName(mistraServiceProperties.getName());
        return mistraService;
    }
}

在创建如下路径文件src/main/resources/META-INF/spring.factories

# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.bamboo.common.autoconfigure.bamboo.BmbooServiceAutoConfiguration
1
2
3
必须是自动配置类的全路径

mvn install 该项目

创建一个springboot-mvc项目pom依赖上面的jar

@SpringBootApplication
@RestController
@Import(value = {CorsConfig.class, LogFilter.class}) //跨域,接口访问请求日志
public class DemoApplication {
	@Autowired
	private BambooServer bmbooService;

	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}
	
	@RequestMapping("/")
	public Object index(){
		return "helll demo"+bmbooService.getName()+DateUtils.getDate();
	}
}









