# Vue-Cli

vue.js有著名的全家桶系列，包含了**vue-router，vuex， vue-resource**，再加上构建工具vue-cli，就是一个完整的vue项目的核心构成。



`vue-cli`这个构建工具大大降低了`webpack`的使用难度，支持热更新，有`webpack-dev-server`的支持，相当于启动了一个请求服务器，给你搭建了一个测试环境，只关注开发就OK。



## 一、安装vue-cli

1.  使用npm（需要安装node环境）全局安装webpack，打开命令行工具输入：`npm install webpack -g`或者（`npm install -g webpack`）安装完成之后输入 `webpack -v`，如下图，如果出现相应的版本号，则说明安装成功。

   **注意：**webpack 4.X 开始，需要安装 webpack-cli 依赖 ,所以使用这条命令  npm install webpack webpack-cli -g

![](https://upload-images.jianshu.io/upload_images/10868449-739f0863e454f1e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/302).

2. 全局安装`vue-cli`，在cmd中输入命令:

```java
npm install --global vue-cli
```

![](https://upload-images.jianshu.io/upload_images/10868449-12cb0bef236c50fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/675)。

**安装成功：**

![](https://upload-images.jianshu.io/upload_images/10868449-ac2a13a62505d1ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/671)。

安装完成之后输入 ``vue -V``（注意这里是大写的“V”），如下图，如果出现相应的版本号，则说明安装成功。

![](https://upload-images.jianshu.io/upload_images/10868449-d8e864620a0b5aef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/292).

打开**C:\Users\zhangwenjie\AppData\Roaming\npm**目录下可以看到：

<img src="C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210312092855206.png" alt="image-20210312092855206" style="zoom: 67%;" />.

打开``node_modules``也可以看到：

<img src="C:\Users\zhangwenjie\AppData\Roaming\Typora\typora-user-images\image-20210312092949173.png" alt="image-20210312092949173" style="zoom:80%;" />。



## 二、用vue-cli来构建项目

1. 我首先在D盘新建一个文件夹（01_vue_code）作为项目存放地，然后使用命令行cd进入到项目目录输入：

```java
vue create baoge
```

**baoge**是自定义的项目名称，命令执行之后，会在当前目录生成一个以该名称命名的项目文件夹。

![](https://upload-images.jianshu.io/upload_images/10868449-22cf1a5dc4cda33d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/659).

输入命令后，会跳出几个选项让你回答：

- `Project name (baoge)：` -----项目名称，直接回车，按照括号中默认名字（注意这里的名字不能有大写字母，如果有会报错Sorry, name can no longer contain capital letters），阮一峰老师博客[为什么文件名要小写](https://link.jianshu.com/?t=http://www.ruanyifeng.com/blog/2017/02/filename-should-be-lowercase.html) ，可以参考一下。
- `Project description (A Vue.js project)：` ----项目描述，也可直接点击回车，使用默认名字
- `Author ()：` ----作者，输入你的大名
  接下来会让用户选择：
- `Runtime + Compiler: recommended for most users` 运行加编译，既然已经说了推荐，就选它了
  Runtime-only: about 6KB lighter min+gzip, but templates (or any Vue-specificHTML) are ONLY allowed in .vue files - render functions are required elsewhere 仅运行时，已经有推荐了就选择第一个了
- `Install vue-router? (Y/n) `是否安装vue-router，这是官方的路由，大多数情况下都使用，这里就输入“y”后回车即可。
- `Use ESLint to lint your code? (Y/n) `是否使用ESLint管理代码，ESLint是个代码风格管理工具，是用来统一代码风格的，一般项目中都会使用。
  接下来也是选择题Pick an ESLint preset (Use arrow keys) 选择一个ESLint预设，编写vue项目时的代码风格，直接y回车
- `Setup unit tests with Karma + Mocha? (Y/n) `是否安装单元测试，我选择安装y回车
- `Setup e2e tests with Nightwatch(Y/n)?` 是否安装e2e测试 ，我选择安装y回车

回答完毕后上图就开始构建项目了。



<img src="https://upload-images.jianshu.io/upload_images/10868449-01a038fa573b22c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/443" style="zoom: 150%;" />。