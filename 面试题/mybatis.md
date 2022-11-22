# mybatis



## 1、Mybatis的Mapper接口方法不能重载

1. 在mapper.xml文件中，接口接口方法是不能重载的。

``` java
List<Dict> selectDictList();
List<Dict> selectDictList(String code);
// 总结：mapper的接口方法是不能重载的
```



##  2、mybatis参数为list时，校验list是否为空？

```xml
// 校验objStatusList 是否为空

<if test="objStatusList != null and objStatusList.size() > 0 ">
　　and
　　<foreach collection="objStatusList" index="objStatusList" item="obj_status" open="(" separator="or" close=")">
　　　　obj_status = #{obj_status}
　　</foreach>
</if>
```





## mybatis哦“



## -4-/*+-缓存机制，从一级缓存到二级缓存？

 缓存，主要作用提高查询效率，减少和数据库交互次数，减轻数据库压力。



## 一级缓存

1. 一级缓存是默认生效的

```java
public static void main(String[] args) throws IOException {
        InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sessionFactory.openSession();
        UserService mapper = sqlSession.getMapper(UserService.class);

        System.out.println("第一次获取对象");
        List<User> userList1 = mapper.getUserList();
        userList1.stream().forEach((x) -> x.setName("张三"));

        System.out.println("第二次获取对象");
        List<User> userList2 = mapper.getUserList();
        System.out.println(userList1 == userList2);
        userList2.stream().forEach((x) -> System.out.println(x.getName()));
}
```



2. 任何更新操作，都会清空一级缓存

```java
public static void main(String[] args) throws IOException {
        InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sessionFactory.openSession();
        UserService mapper = sqlSession.getMapper(UserService.class);

        System.out.println("第一次获取对象");
        List<User> userList1 = mapper.getUserList();
        userList1.stream().forEach((x) -> x.setName("张三"));

        System.out.println("第二次获取对象");
        List<User> userList2 = mapper.getUserList();
        System.out.println(userList1 == userList2);
        userList2.stream().forEach((x) -> System.out.println(x.getName()));

        // 添加数据
        User user = new User();
        user.setName("lisi");
        mapper.insert(user);

        System.out.println("第三次获取对象");
        List<User> userList3 = mapper.getUserList();
        System.out.println(userList2 == userList3);//出现false，会从数据库在获取一遍
}
```

一级缓存总结：

1. 一级缓存模式默认是：开启
2. 一级缓存作用域在于sqlSession（大家可以关闭SqlSession，然后创建一个新的，再获取对象，观察结果）
3. 如果中间有对数据的更新操作，则将清空一级缓存。