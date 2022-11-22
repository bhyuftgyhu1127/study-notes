# 05-mybatis-plus-wrapper`(重点)`

![image-20210310135221084](C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210310135221084.png)

wrapper：条件构造抽象类，最顶端父类

- AbstracWrapper：用于查询条件封装，生成sql的where条件

  - updateWrapper：Update 条件封装，用于Entity对象更新操作
  - QueryWrapper：Entity 对象封装操作类，不是用lambda语法

  - AbstractLambdaWrapper： Lambda 语法使用 Wrapper统一处理解析 lambda 获取 column。
    - lambdaUpdateWrapper：Lambda 更新封装Wrapper
    - LambdaQueryWrapper：看名称也能明白就是用于Lambda语法使用的查询Wrapper

  

  

  ```
  /**
   * AbstractWrapper
   * 注意：以下条件构造器的方法入参中的 column 均表示数据库字段
   */
  @Test
  public void AbstractWrapper() {
      QueryWrapper<User> queryWrapper = new QueryWrapper<>();
      queryWrapper
              .isNull("name")
              // ge：大于
              // eq：等于
              // gt：小于
              .ge("age", 12)
              .isNotNull("email");
      int delete = userMapper.delete(queryWrapper);
      System.out.println("delete = " + delete);
  }
  ```

![](https://img-blog.csdnimg.cn/20190710003132794.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzE2NjIyNw==,size_16,color_FFFFFF,t_70).