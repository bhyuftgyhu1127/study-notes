# EasyExcle



## 一、添加依赖

```java
<!--alibaba easyexcel-->
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>easyexcel</artifactId>
  <version>1.1.2-beta5</version>
</dependency>
```



## 二、EasyExcel使用（写操作）

### 1、实体类

```java
/**
 * @author zhangwenjie
 * @date 2021/03/11
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User implements Serializable {

    @ExcelProperty("用户编号")
    private Long userId;
    @ExcelProperty("姓名")
    private String userName;
    @ExcelProperty("性别")
    private String gender;
    @ExcelProperty("工资")
    private Double salary;
    @ExcelProperty("入职时间")
    private Date hireDate;

}
```

### 2、最简单的写**(方式一)**

```java
@Test
public void Test01() {
    // 创建文件保存位置
    String fileName = "user01.xlsx";

    /**
     * 构建要写入的数据
     * User类是一个自定义的特殊类,专门用来构建向Excel中写数据的类型类
     * @ExcelProperty是easyexcel提供的注解,用来定义表格的头部
     */
    List<User> users = new ArrayList<>();
    User user = new User(1L,"小宁","男",100.0,new Date());
    User user2 = new User(1L,"小宁","男",100.0,new Date());
    User user3 = new User(1L,"小宁","男",100.0,new Date());
    users.add(user);
    users.add(user2);
    users.add(user3);
    EasyExcel.write(fileName,User.class).sheet("用户信息").doWrite(users);

}
```

### 3、最简单的写**(方式二)**

```java
@Test
public void Test02() {
    // 创建文件保存位置
    String fileName = "user01.xlsx";

    /**
     * 构建要写入的数据
     * User类是一个自定义的特殊类,专门用来构建向Excel中写数据的类型类
     * @ExcelProperty是easyexcel提供的注解,用来定义表格的头部
     */
    List<User> users = new ArrayList<>();
    User user = new User(1L, "小宁", "男", 100.0, new Date());
    User user2 = new User(1L, "小宁", "男", 100.0, new Date());
    User user3 = new User(1L, "小宁", "男", 100.0, new Date());
    users.add(user);
    users.add(user2);
    users.add(user3);
    // 创建Excel写对象
    ExcelWriter writerBuilder = EasyExcel.write(fileName, User.class).build();
    // 创建sheet对象
    WriteSheet sheet = EasyExcel.writerSheet("用户信息").build();
    //将数据写到sheet标签中
    writerBuilder.write(users, sheet);
    // 关闭流，文件流关闭
    writerBuilder.finish();
}
```

### 4、将类中某几个字段排除掉,不写入Excel中

```java
@Test
public void Test03() {
    String fileName = "user01.xlsx";

    List<User> users = new ArrayList<>();
    User user = new User(1L, "小宁", "男", 100.0, new Date());
    User user2 = new User(1L, "小宁", "男", 100.0, new Date());
    User user3 = new User(1L, "小宁", "男", 100.0, new Date());
    users.add(user);
    users.add(user2);
    users.add(user3);

    // 需要不包含的字段名称 
    HashSet<String> hashSet = new HashSet<>();
    hashSet.add("hireDate");
    hashSet.add("userId");
    hashSet.add("salary");
    
	// includeColumnFiledNames：包含那几个字段
    // excludeColumnFiledNames:不包含那几个字段
    // EasyExcel.write(fileName,User.class).includeColumnFiledNames(hashSet).sheet("工资").doWrite(users);
    EasyExcel.write(fileName,User.class).excludeColumnFiledNames(hashSet).sheet().doWrite(users);
}
```

### 5、 插入指定的列

**将Java对象中指定的属性,插入到Excel表格中的指定的列(在Excel中进行列排序)**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class IndexUser {
    /**
     * value: 设置Excel中列头的名字 index: 设置Excel中将数据插入指定的列(列排序) eg: value="姓名" index=2 :
     * 将姓名的列头设置在Excel表格的第三列
     */
    @ExcelProperty(value = "用户编号", index = 0)
    private Integer userId;
    @ExcelProperty(value = "姓名", index = 2)
    private String userName;
    @ExcelProperty(value = "入职时间", index = 1)
    private Date hireDate;
}
```

```java
@Test
public void Test05() {
    String fileName = "user01.xlsx";

    List<IndexUser> users = new ArrayList<>();
    IndexUser user = new IndexUser(1, "小宁", new Date());
    IndexUser user2 = new IndexUser(1, "小宁",  new Date());
    IndexUser user3 = new IndexUser(1, "小宁",  new Date());
    users.add(user);
    users.add(user2);
    users.add(user3);

    EasyExcel.write(fileName,IndexUser.class).sheet().doWrite(users);
}
```

## 三、EasyExcel使用（读操作）

- Excel表格数据类型

[![img](https://note.youdao.com/yws/api/personal/file/WEBd0b986a78d3129445d7cb5c5d8ae148d?method=download&shareKey=d499346d2c26e9eef3c36b363302f099)](https://note.youdao.com/yws/api/personal/file/WEBd0b986a78d3129445d7cb5c5d8ae148d?method=download&shareKey=d499346d2c26e9eef3c36b363302f099)

- 盛装数据的Java对象

  ```
  /**
   * 承装Excel表格数据的类
   * 注意点: Java类中的属性的顺序和Excel中的列头的顺序是相同的
   * @Author 夜泊 
   * @BLOG   https://hd1611756908.github.io/
   */
  public class DemoData {
      
  	private String name;
  	private Date hireDate;
  	private Double salary;
  }
  ```

- 读取Excel数据

  ```java
  /*
   * 最简单的读取Excel内容(方式一)
   * 默认读取Excel文件中的第一个sheet
   */
  @Test
  public void testRead1() {
  	//读取的文件路径
  	String fileName="user11.xlsx";
  	Class<DemoData> head = DemoData.class; //创建一个数据格式来承装读取到数据
  	//读取数据
  	EasyExcel.read(fileName, head, new AnalysisEventListener<DemoData>() {
  
  		/**
  		 * 解析每一条数据的时候被调用
  		 */
  		@Override
  		public void invoke(DemoData data, AnalysisContext context) {
  			//在这里操作，将解析的每一条数据保存到数据库中,在这里可以调用数据库
  			System.out.println("解析的数据为: "+data);
  		}
  
  		/**
  		 * 解析完所有数据的时候被调用
  		 */
  		@Override
  		public void doAfterAllAnalysed(AnalysisContext context) {
  			System.out.println("数据解析完成..........");
  		}
  	}).sheet().doRead();
  	
  }
  ```

```java
/*
 * 最简单的读取Excel内容(方式二)
 * 默认读取Excel文件中的第一个sheet
 */
@Test
public void readExcle2() {
    ExcelReader build = EasyExcel.read(fileName, head, new AnalysisEventListener<DemoData>() {

        @Override
        public void invoke(DemoData demoData, AnalysisContext analysisContext) {
            System.out.println("demoData = " + demoData);
        }

        @Override
        public void doAfterAllAnalysed(AnalysisContext analysisContext) {
            System.out.println("-------------结束");
        }
    }).build();
    ReadSheet readSheet = EasyExcel.readSheet(0).build();
    build.read(readSheet);
    build.finish();

}
```



## 四、Web

```java
package com.cslc.controller;

import com.alibaba.excel.EasyExcel;
import com.alibaba.excel.context.AnalysisContext;
import com.alibaba.excel.event.AnalysisEventListener;
import com.cslc.demo.User;
import org.apache.tomcat.util.http.fileupload.FileItem;
import org.apache.tomcat.util.http.fileupload.FileUploadException;
import org.apache.tomcat.util.http.fileupload.RequestContext;
import org.apache.tomcat.util.http.fileupload.disk.DiskFileItemFactory;
import org.apache.tomcat.util.http.fileupload.servlet.ServletFileUpload;
import org.apache.tomcat.util.http.fileupload.servlet.ServletRequestContext;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.multipart.MultipartHttpServletRequest;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.InputStream;
import java.net.URLEncoder;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

/**
 * @author zhangwenjie
 * @date 2021/03/11
 */
@Controller
public class DownloadController {

    public static final String fileName = "demo.xlsx";

    /**
     * EasyExcel下载步骤
     */
    @GetMapping("/DownLoadExcel")
    public void download(HttpServletRequest request, HttpServletResponse response) throws IOException {
        // 设置响应头
        response.setContentType("application/vnd.ms-excel");
        response.setCharacterEncoding("utf-8");

        //设置防止文件名中文乱码
        String encode = URLEncoder.encode("utf-8");
        response.setHeader("Content-disposition", "attachment;filename=" + fileName);

        //构建写入到Excel中的数据(此数据可以从数据库中获取)
        List<User> users = new ArrayList<>();
        User user1 = new User(1001, "李雷1", "男", new Date());
        User user2 = new User(1002, "李雷2", "男", new Date());
        User user3 = new User(1003, "李雷3", "男", new Date());
        users.add(user1);
        users.add(user2);
        users.add(user3);
        EasyExcel.write(response.getOutputStream(), User.class).sheet("用户信息").doWrite(users);
    }

    @PostMapping("/UploadExcelServlet")
    public void UploadExcelServlet(HttpServletRequest request, HttpServletResponse resp) throws ServletException, IOException {
        DiskFileItemFactory factory = new DiskFileItemFactory();
        ServletFileUpload fileUpload = new ServletFileUpload(factory);
        //设置单个文件为3M
        fileUpload.setFileSizeMax(1024 * 1024 * 3);
        //总文件大小为30M
        fileUpload.setSizeMax(1024 * 1024 * 3 * 10);
        try {
            List<FileItem> list = fileUpload.parseRequest(new ServletRequestContext(request));

            for (FileItem fileItem : list) {

                //判断是否为附件
                if (!fileItem.isFormField()) {
                    //是附件
                    InputStream inputStream = fileItem.getInputStream();
                    EasyExcel.read(inputStream, User.class, new AnalysisEventListener<User>() {

                        @Override
                        public void doAfterAllAnalysed(AnalysisContext arg0) {
                            System.out.println("Excel全部读完被执行......");
                        }

                        @Override
                        public void invoke(User data, AnalysisContext arg1) {
                            //读完一行就执行一次(调用数据库进行插入操作)
                            System.out.println("解析一行: " + data);
                        }
                    }).sheet().doRead();
                } else {
                    //普通表单
                }
            }
        } catch (FileUploadException e) {
            e.printStackTrace();
        }


    }

}
```

### 前端

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div>
    <form th:action="@{/UploadExcelServlet}" method="post" enctype="multipart/form-data">
        <input type="file" name="file"><br>
        <input type="submit" value="上传">
    </form>

    <a th:href="@{/DownLoadExcel}">下载</a>
</div>
</body>
</html>
```

```properties
spring.groovy.template.cache=false
spring.http.multipart.enabled=false
spring.servlet.multipart.enabled=false
```