# 02-mybatis-plus--demo



## 一、创建并初始化数据库

### 1、创建数据库

`mybatis_plus`

### 2、创建user表

其表结构如下：

| id   | name   | age  | email              |
| ---- | ------ | ---- | ------------------ |
| 1    | Jone   | 18   | test1@baomidou.com |
| 2    | Jack   | 20   | test2@baomidou.com |
| 3    | Tom    | 28   | test3@baomidou.com |
| 4    | Sandy  | 21   | test4@baomidou.com |
| 5    | Billie | 24   | test5@baomidou.com |

其对应的数据库 Schema 脚本如下：

```sql
DROP TABLE IF EXISTS user;

create table user
(
  id bigint(20) NOT NULL COMMENT '主键ID',
  name varchar(30) NULL DEFAULT NULL COMMENT '姓名',
  age int(11) NULL DEFAULT NULL COMMENT '年龄',
  email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
  PRIMARY KEY (id)
);
```



其对应的数据库 Data 脚本如下：

```sql
DELETE FROM user;

INSERT INTO user VALUES(1,'小明',11,'bhyuftgyhu@qq.com');
INSERT INTO user VALUES(2,'小东',20,'804308041@qq.com');
INSERT INTO user VALUES(3,'小王',30,'1622751774@qq.com');
```



## 二、初始化工程

使用 `Spring Initializr` 快速初始化一个 `Spring Boot `工程

Group：`com.atguigu`

Artifact：`mybatis-plus`

版本：**2.2.1.RELEASE**



## 三、添加依赖



### 1、引入依赖

`spring-boot-starter、spring-boot-starter-test`

添加：`mybatis-plus-boot-starter、mysql、lombok`

在项目中使用Lombok可以减少很多重复代码的书写。比如说`getter/setter/toString`等方法的编写

```java
<dependencies>
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter</artifactId>
   </dependency>

   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
      <exclusions>
          <exclusion>
             <groupId>org.junit.vintage</groupId>
              <artifactId>junit-vintage-engine</artifactId>
          </exclusion>
      </exclusions>
   </dependency>


  <!--mybatis-plus-->
  <dependency>
      <groupId>com.baomidou</groupId>
      <artifactId>mybatis-plus-boot-starter</artifactId>
      <version>3.0.5</version>
  </dependency>

  <!--mysql-->
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
  </dependency>


  <!--lombok用来简化实体类-->
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
  </dependency>
</dependencies>
```

**注意：**引入 `MyBatis-Plus` 之后请不要再次引入 `MyBatis` 以及 `MyBatis-Spring`，以避免因版本差异导致的问题。



### 2、配置

在 `application.properties` 配置文件中添加 MySQL 数据库的相关配置：

mysql5

```properties
#mysql数据库连接
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis_plus
spring.datasource.username=root
spring.datasource.password=123456
```

mysql8以上（spring boot 2.1）

**注意：**driver和url的变化

**注意：**

1、这里的 `url` 使用了 `?serverTimezone=GMT%2B8 `后缀，因为`Spring Boot 2.1 `集成了 8.0版本的jdbc驱动，这个版本的 jdbc 驱动需要添加这个后缀，否则运行测试用例报告如下错误：

`java.sql.SQLException: The server time zone value 'ÖÐ¹ú±ê×¼Ê±¼ä' is unrecognized or represents more `

2、这里的 `driver-class-name `使用了 `com.mysql.cj.jdbc.Driver `，在 `jdbc 8 `中 建议使用这个驱动，之前的 `com.mysql.jdbc.Driver `已经被废弃，否则运行测试用例的时候会有 WARN 信息



### 3、编写代码

#### 1、主类

在 Spring Boot 启动类中添加 `@MapperScan` 注解，扫描 Mapper 文件夹

**注意：**扫描的包名根据实际情况修改

```java
@SpringBootApplication
@MapperScan(basePackages = "com.cslc.dao")
public class MybatisplusDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(MybatisplusDemoApplication.class, args);
    }

}
```



#### 2、实体

创建包 entity 编写实体类 `User.java`（此处使用了 [Lombok](https://www.projectlombok.org/) 简化代码）

```java
@Data
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

查看编译结果：

<img src="C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210310101002839.png" alt="image-20210310101002839" style="zoom:67%;" />.



#### 3、mapper

创建包 mapper 编写Mapper 接口： `UserMapper.java`

```java
public interface UserMapper extends BaseMapper<User> {

}
```



#### 4、测试

添加测试类，进行功能测试：

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class TestDemo {
    @Autowired
    private UserMapper userMapper;
    
    @Test
    public void testUser01() {
        // UserMapper 中的 selectList() 方法的参数为 MP 内置的条件封装器 Wrapper
        // 所以不填写就是无任何条件
        List<User> users = userMapper.selectList(null);
        users.forEach(System.out::print);
    }
    
    /**
     * 根据id查询记录
     */
    @Test
    public void testUser02() {
        // SELECT id,name,age,email FROM user WHERE id=? 
        // Parameters: 1(Long)
        User user = userMapper.selectById(1L);
        System.out.println(user);
    }
    
     /**
     * 通过多个id批量查询
     */
    @Test
    public void testUser03() {
        // SELECT id,name,age,email FROM user WHERE id IN ( ? , ? , ? ) 
        // Parameters: 1(Long), 2(Long), 3(Long)
        List<User> users = userMapper.selectBatchIds(Arrays.asList(1l, 2L, 3L));
        users.forEach(System.out::print);
    }
    
     /**
     * 简单的条件查询
     */
    @Test
    public void testUser04() {
        HashMap<String, Object> map = new HashMap<>();
        map.put("name","小明");
        map.put("age",11);
        // Preparing: SELECT id,name,age,email FROM user WHERE name = ? AND age = ? 
        // Parameters: 小明(String), 11(Integer)
        // 注意：map中的key对应的是数据库中的列名。例如数据库user_id，实体类是userId，这时map的key需要填写user_id
        List<User> users = userMapper.selectByMap(map);
        users.forEach(System.out::print);
    }
}
```

**注意：**

IDEA在 userMapper 处报错，因为找不到注入的对象，因为类是动态创建的，但是程序可以正确的执行。

为了避免报错，可以在 dao 层 的接口上添加 @Repository 注



#### 5、配置日志

查看sql输出日志：

```properties
#mybatis日志
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

