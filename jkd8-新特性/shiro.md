# shiro

1、导入依赖

2、配置文件

3、HelloWord

```java
// 获取当前的用户对象Subject
subject currentUser = SecurityUtils.getSubject();
// 通过当前用户拿到session
Session session = currentUser.getSession();
// 判断当前的用户是否被认证
currentUser.isAuthenticated()
// 获得当前用户的认证
currentUser.getPrincipal()
// 获取这个用户拥有什么角色
currentUser.hasRole()
// 获取当前用户的权限
currentUser.isPermitted()
// 注销用户
currentUser.logout()
```



### Springboot中集成

1、依赖

```java
	 <!--thymeleaf-->
        <dependency>
            <groupId>org.thymeleaf</groupId>
            <artifactId>thymeleaf-spring5</artifactId>
        </dependency>
        <dependency>
            <groupId>org.thymeleaf.extras</groupId>
            <artifactId>thymeleaf-extras-java8time</artifactId>
        </dependency>
         <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring</artifactId>
            <version>1.3.2</version>
        </dependency>
```



![image-20201221225901746](C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20201221225901746.png).

###### Subject:用户

###### SecurityManager:管理所有用户

###### Realm：连接数据







