# 03-mybatis-plus-分页.md



## 一、分页

MyBatis Plus自带分页插件，只要简单的配置即可实现分页功能

**（1）创建配置类**

此时可以删除主类中的 *@MapperScan* 扫描注解

```java
@Configuration
@MapperScan("com.cslc.dao")
public class MybatisPlusConfig {

    /**
     * 分页插件
     */
    @Bean
    public PaginationInterceptor PaginationInterceptor() {
        PaginationInterceptor interceptor = new PaginationInterceptor();
        return interceptor;
    }
}
```

**（2）测试selectPage分页**

**测试：**最终通过page对象获取相关数据

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class TestDemo {    
    
	@Test
    public void pageTests() {
        Page<User> page = new Page<>(1,1);
        userMapper.selectPage(page, null);
        page.getRecords().forEach(System.out::println);
        // 当前页
        System.out.println(page.getCurrent());
        // 页数
        System.out.println(page.getPages());
        // 当前个数
        System.out.println(page.getSize());
        // 当前条数
        System.out.println(page.getTotal());
        // 是否有下一个
        System.out.println(page.hasNext());
        // 是否有上一个
        System.out.println(page.hasPrevious());
    }
    
}
```

控制台sql语句打印：`SELECT id,name,age,email,create_time,update_time FROM user LIMIT 0,5 `

**（3）map分页**

```
/**
 * 分页map
 */
@Test
public void testUser06() {
    Page<User> page = new Page<>();
    IPage<Map<String, Object>> mapIPage = userMapper.selectMapsPage(page, null);
    // 注意：此行必须使用 mapIPage 获取记录列表，否则会有数据类型转换错误
    mapIPage.getRecords().forEach(System.out::print);
    System.out.println("mapIPage.getRecords() = " + mapIPage.getRecords());
    System.out.println("mapIPage.getCurrent() = " + page.getCurrent());
    System.out.println("mapIPage.getPages() = " + page.getPages());
    System.out.println("mapIPage.getSize() = " + page.getSize());
    System.out.println("mapIPage.getTotal() = " + page.getTotal());
}
```



## 二、delete

###  **1、根据id删除记录**

```java
@Test
public void testUser07() {
    int id = userMapper.deleteById(1L);
    System.out.println("id = " + id);
}
```

### 2、批量删除记录

```java
@Test
public void testUser08() {
    int i = userMapper.deleteBatchIds(Arrays.asList(2L, 3L));
    System.out.println(i);
}
```

### **3、条件查询删除**

```java
@Test
public void testUser09() {
    HashMap<String, Object> hashMap = new HashMap<>();
    hashMap.put("name","小明");
    hashMap.put("age",20L);
    int i = userMapper.deleteByMap(hashMap);
    System.out.println("i = " + i);
}
```