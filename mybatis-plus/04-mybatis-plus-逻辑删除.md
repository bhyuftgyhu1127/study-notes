# 04-mybatis-plus-逻辑删除



## 一、逻辑删除

- **物理删除：**真实删除，将对应数据从数据库中删除，之后查询不到此条被删除数据
- **逻辑删除：**假删除，将对应数据中代表是否被删除字段状态修改为“被删除状态”，之后在数据库中仍旧能看到此条数据记录



1、**数据库中添加 deleted字段**

```sql
alter table `表名` add column `字段` 类型;
```

2、**实体类(user)添加deleted 字段**

并加上 `@TableLogic `注解 和 `@TableField(fill = FieldFill.INSERT)`注解

```java
/**
 * 删除标志
 */
@TableLogic
@TableField(fill = FieldFill.INSERT)
private boolean deleted;
```

3、**元对象处理器接口添加deleted的insert默认值**

```java
package com.cslc.handlers;

import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
import org.apache.ibatis.reflection.MetaObject;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

import java.util.Date;

/**
 * @fileName:com.cslc.handlers
 * @author:zhangwenjie
 * @date:2021/01/31
 */
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {
    /**
     * 插入自动默认值为 0
     * @param metaObject
     */
    @Override
    public void insertFill(MetaObject metaObject) {
        this.setFieldValByName("deleted", 0, metaObject);
    }

    /**
     * 更新自动默认值为 1
     * @param metaObject
     */
    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("updateTime", new Date(), metaObject);
        this.setFieldValByName("deleted", 1, metaObject);
    }
}
```

4、**application.properties 加入配置**

此为默认值，如果你的默认值和mp默认的一样,该配置可无

```properties
mybatis-plus.global-config.db-config.logic-delete-value=1
mybatis-plus.global-config.db-config.logic-not-delete-value=0
```

5、**在 MybatisPlusConfig 中注册 Bean**

```java
@Configuration
@MapperScan("com.cslc.dao")
public class MybatisPlusConfig {
    /**
     * 逻辑删除插件
     */
    @Bean
    public ISqlInjector iSqlInjector() {
        return new LogicSqlInjector();
    }
}
```

6、**测试逻辑删除**

- 测试后发现，数据并没有被删除，deleted字段的值由0变成了1
- 测试后分析打印的sql语句，是一条update
- **注意：**被删除数据的deleted 字段的值必须是 0，才能被选取出来执行逻辑删除的操作

```java
@Test
public void testUser10() {
    int i = userMapper.deleteById(2l);
    System.out.println("i = " + i);
}
```

7、**测试逻辑删除后的查询**

MyBatis Plus中查询操作也会自动添加逻辑删除字段的判断

```java
/**
 * 测试 逻辑删除后的查询：
 * 不包括被逻辑删除的记录
 */
@Test
public void testUser11() {
    // SELECT id,name,age,email,deleted FROM user WHERE deleted=0
    List<User> users = userMapper.selectList(null);
    users.forEach(System.out::print);
}
```