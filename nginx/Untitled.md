# Nginx

# 一、nginx简介

## 1. 什么是 nginx 和可以做什么事情

- Nginx 是高性能的 HTTP 和反向代理的web服务器，处理高并发能力是十分强大的，能经受高负 载的考验,有报告表明能支持高达 50,000 个并发连接数。
- 其特点是占有内存少，并发能力强，事实上nginx的并发能力确实在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、京东、新浪、网易、腾讯、淘宝等。



## 2.Nginx 作为 web 服务器

- Nginx 可以作为静态页面的 web 服务器，同时还支持 CGI 协议的动态语言，比如 perl、php 等。但是不支持 java。Java 程序只能通过与 tomcat 配合完成。Nginx 专为性能优化而开发， 性能是其最重要的考量,实现上非常注重效率 ，能经受高负载的考验,有报告表明能支持高 达 50,000 个并发连接数。



## 3. 正向代理

Nginx 不仅可以做反向代理，实现负载均衡。还能用作正向代理来进行上网等功能。

- **正向代理：如果把局域网外的 Internet 想象成一个巨大的资源库，则局域网中的客户端要访 问 Internet，则需要通过代理服务器来访问，这种代理服务就称为正向代理。**

  - 简单一点：通过代理服务器来访问服务器的过程 就叫 正向代理。
  - 需要在客户端配置代理服务器进行指定网站访问

  

## 4. 反向代理

- 反向代理，**其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问**。

- **我们只 需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，在返 回给客户端**，此时反向代理服务器和目标服务器对外就是一个服务器，**暴露的是代理服务器 地址，隐藏了真实服务器 IP 地址。**



## 5. 负载均衡

- 增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的 情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的`负载均衡`

- 客户端发送**多个请求到服务器**，服务器处理请求，有一些可能要与数据库进行交互，服 务器处理完毕后，再将结果返回给客户端。





<img src="C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210715133631179.png" alt="image-20210715133631179" style="zoom:67%;" />.

<img src="C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210715133705202.png" alt="image-20210715133705202" style="zoom:67%;" />。

## 6.动静分离

为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速度。降低原来单个服务器的压力。

<img src="C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210715133804348.png" alt="image-20210715133804348" style="zoom:50%;" />.



# 二、Nginx 的安装(Linux:centos为例)



## 1. 准备工作

- 打开虚拟机，使用finallshell链接Linux操作系统

- 到nginx下载软件
  http://nginx.org/

- 先安装其依赖软件，最后安装nginx。

- 下载链接：https://www.aliyundrive.com/s/Z1TXVkxNz5f



## 2. 开始安装

- 都有两种方式，一种直接下载，第二种使用解压包方式。这里大多使用解压包方式。
- **我的安装路径：/usr/feng/**



1. **安装pcre**

   方式一、wget http://nginx.org/download/nginx-1.20.1.tar.gz

   方拾二、上传源码压缩包，解压、编译、安装 三部曲。

   1）、解压文件， 进入pcre目录，
   2）、./configure 完成后，
   3）、执行命令： make && make install

2. **安装 openssl**

   下载OpenSSL的地址:
   http://distfiles.macports.org/openssl/

   1）、解压文件， 回到 openssl目录下，
   2）、./configure 完成后，
   3）、执行命令： make && make install

3. **安装 zlib**
   1）、解压文件， 回到 zlib 目录下，
   2）、./configure 完成后，
   3）、执行命令： make && make install

2. **安装 nginx **
   1）、解压文件， 回到 nginx 目录下，
   2）、./configure 完成后，
   3）、执行命令： make && make install



## 3. 运行nginx

- 安装完nginx后，会在 路径 /usr/local 下自动生成 nginx 文件夹。这是自动生成的。

- 进入这个目录：

```shell
cd /usr/local/nginx
```

目录内容如下:

![image-20210715135558916](C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210715135558916.png)。



- 进入sbin文件夹,里面有两个文件：nginx 和 nginx.old。
- 执行命令：**./nginx** 即可执行
- 测试启动： **ps -ef | grep nginx**

![image-20210715135622165](C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210715135622165.png).

已经启动。



- 查看nginx默认端口（默认为80），使用网页的形式测试，（像Tomcat一样。）

- **进入目录查看端口：cd /usr/local/nginx/conf 下的 nginx.conf文件**。这个文件也是nginx的配置文件。vim 下：

![image-20210715135702033](C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210715135702033.png).

如下：

- 输入IP:80，则显示：

<img src="C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210715135730730.png" alt="image-20210715135730730" style="zoom: 50%;" />.



## 4. 防火墙问题

在 windows 系统中访问 linux 中 nginx，默认不能访问的，因为防火墙问题 （1）关闭防火墙 （2）开放访问的端口号，80 端口



查看开放的端口号:

```shell
firewall-cmd --list-all 
```

设置开放的端口号

```shell
firewall-cmd --add-service=http –permanent 
firewall-cmd --add-port=80/tcp --permanent 
```

重启防火墙

```shell
firewall-cmd –reload 
```



# 三、 Nginx 的常用命令和配置文件

## 1. Nginx常用命令

### a. 使用nginx操作命令前提

使用nginx操作命令前提：**必须进入到nginx的自动生成目录的下/sbin文件夹下**。

nginx有两个目录：

**第一个**：安装目录，我放在：

```java
/usr/feng/
```

**第二个**：自动生成目录：

```shell
/usr/local/nginx/
```



### b. 查看 nginx 的版本号

```java
./nginx -v
```



### c. 启动 nginx

```shell
./nginx	
```



### d. 关闭nginx

```shell
./nginx -s stop
```



### e. 重新加载 nginx

在目录：**/usr/local/nginx/sbin 下执行命令**，不需要重启服务器，自动编译。

```shell
./nginx -s reload
```



## 2. Nginx配置文件

### a. 配置文件位置

/usr/local/nginx/conf/nginx.conf

<img src="C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210715140239015.png" alt="image-20210715140239015" style="zoom:67%;" />.



### b. nginx 的组成部分

配置文件中有很多#， 开头的表示注释内容，我们去掉所有以 # 开头的段落，精简之后的 内容如下：

```java
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}

```

- nginx 配置文件有三部分组成



#### 第一部分：全局块

从配置文件开始到 events 块之间的内容，主要会设置一些影响nginx 服务器整体运行的配置指令，主要包括配 置运行 Nginx 服务器的用户（组）、允许生成的 worker process 数，进程 PID 存放路径、日志存放路径和类型以 及配置文件的引入等。

比如上面第一行配置的：

```shell
  worker_processes  1;
```

这是 Nginx 服务器并发处理服务的关键配置，worker_processes 值越大，可以支持的并发处理量也越多，但是 会受到硬件、软件等设备的制约。



#### 第二部分：events块

比如上面的配置：

```shell
events {
    worker_connections  1024;
}
```

events (事件)块涉及的指令**主要影响 Nginx 服务器与用户的网络连接，常用的设置包括是否开启对多 work process 下的网络连接进行序列化，是否 允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个 word process 可以同时支持的最大连接数等。**
上述例子就表示每个 work process 支持的最大连接数为 1024.
这部分的配置对 Nginx 的性能影响较大，在实际中应该灵活配置。

#### 第三部分：

```shell
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

这算是 Nginx 服务器配置中最频繁的部分，代理、缓存和日志定义等绝大多数功能和第三方模块的配置都在这里。

需要注意的是：http 块也可以包括 http全局块、server 块。

- **http全局块**
  http全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等。
- **server 块**
  这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了 节省互联网服务器硬件成本。

**每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。**
**而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。**



1. **全局 server 块**
   最常见的配置是本虚拟机主机的监听配置和本虚拟主机的名称或IP配置。

2. **location 块**
   一个 server 块可以配置多个 location 块。
   这块的主要作用是**基于 Nginx 服务器接收到的请求字符串（例如 server_name/uri-string），对虚拟主机名称 （也可以是IP 别名）之外的字符串（例如 前面的 /uri-string）进行匹配，对特定的请求进行处理**。 地址定向、数据缓 存和应答控制等功能，还有许多第三方模块的配置也在这里进行。



# 四、 Nginx 反向代理 配置实例 1.1



## 1. 实现效果

- 打开浏览器，在浏览器地址栏输入地址 www.123.com，跳转到 liunx 系统 tomcat 主页 面中

## 2. 准备工作

1）在 liunx 系统安装 tomcat，使用默认端口 8080，我这里8080被其他应用占用，所以我已修改端口为8081。在conf目录下的server.xml配置文件中，如下，将port改为 8081，其实下面也有类似的Connector 标签，但是要看protocol协议为HTTP/1.1的标签修改即可。

```xml
<Connector port="8081" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```

- tomcat 安装文件放到 liunx 系统中，解压。
  **Tomcat的路径**：/usr/feng/apach-tomcat/tomcat8081下
- 进入 tomcat 的 bin 目录中，./startup.sh 启动 tomcat 服务器。



（2）**对外开放访问的端口** （我这里不需要）

- firewall-cmd --add-port=8080/tcp --permanent
- firewall-cmd –reload
- 查看已经开放的端口号 firewall-cmd --list-all



​	(3) **在 windows 系统中通过浏览器访问 tomcat 服务器**
别忘了开启tomcat，在bin目录下，使用 命令：

```shell
./startup.sh
```



## 3. 访问过程的分析

![image-20210715142055445](C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210715142055445.png)。



## 4、具体配置

### a. 第一步 在 windows 系统的 host 文件进行域名和 ip 对应关系的配置

![image-20210715142121941](C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210715142121941.png).

**添加内容在 host 文件中**

![image-20210715142135438](C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210715142135438.png)。



### 2. 第二步 在 nginx 进行请求转发的配置（反向代理配置）

![image-20210715142153554](C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210715142153554.png).



## 5、最终测试

**如上配置，我们监听 80 端口，访问域名为 www.123.com，不加端口号时默认为 80 端口，故 访问该域名时会跳转到 127.0.0.1:8081 路径上。在浏览器端输入 www.123.com 结果如下：**

<img src="C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210715142254231.png" alt="image-20210715142254231" style="zoom:80%;" />

