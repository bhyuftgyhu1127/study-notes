# Vue(第一天)

HTML+CSS+JS 视图 `给用户看，刷新后台的数据`



网络通讯：axios

页面跳转：vue-router

状态管理：vuex

Vue-UI：飞冰



## javaScript

- jQuery：优点是简化Dom操作
- Angular：Goole开发，mvc搬到了前端
- React：Facebook的，特点，虚拟Dom
- vue：渐进式JavaScript的框架，集成了Angular模块化和React（虚拟Dom）
- Axios：前端通信框架；就是为了处理Dom



## UI框架

- Ant-Design：阿里巴巴，基于React的UI框架
- Element-UI、iview、ice：饿了么出品，基于vue的ui框架
- BootStrap：Twitter推出的一个用于前端开发的开源工具包
- AmazeUI：又叫“妹子UI”，一款HTML5的前端框架



## JavaScript构建工具：

Babel：js编译工具

WePack：模块打包器，主要作用的是打包、压缩、合并及按序加载



## 三端统一

### 混合开发

主要目的的实现一套代码三端统一并能够用到设备底层硬件，打包方式主要有以下两种：

- 云打包：HBuil-->HBuil X-->DCloud出品
- 本地打包：Cordova



## 	View-UI

[官网](https://www.uviewui.com/)



## Element-UI

[官网](https://element.eleme.cn/#/zh-CN)



## 飞冰

[官网](https://ice.work/)



## 什么是MVVM

MVVM是一种软件架构设计模式。

MVVM源自于经典的mvc模式。MVVM的核心是ViewModel层，负责转换Model中数据对象来让数据变得更容易管理和使用。

- 该层向上与视图层进行双向数据绑定

- 向下与model层通过接口请求进行数据交互

  <img src="D:\学习笔记\imge\vue.png" style="zoom:80%;" />.



## 第一个Vue程序

【说明】IDEA可以安装vue插件



### 下载地址

- CSDN

  ```html
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  ```

 ## 绑定

- **bind**

```html
<div id="app">
    <p v-bind:title="type"> 鼠标悬停几秒钟查看此处动态绑定的提示信息！</p>
</div>

<!--导入vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    let vue = new Vue({
        el: "#app",
        data: {
            type: '男'
        }
    });
</script>	
```



## 判断--循环

- **if**

```html
<div id="app">
    <p v-if="type =='a' ">aaaa</p>
    <p v-else-if="type =='b' ">bbbb</p>
    <p v-else="type">cccc</p>
</div>

<!--导入vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    let vue = new Vue({
        el: "#app",
        data: {
            type: "a"
        }
    });
</script>
```

- **for**

  ```html
  <div id="app">
     <p v-for="(item,index) in items">
         {{item.message}}---{{index}}
     </p>
      <p>
          {{type.name}}
      </p>
  
  </div>
  
  <!--导入vue.js-->
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script>
      let vue = new Vue({
          el: "#app",
          data:{
              items:[
                  {message: 'a'},
                  {message: 'b'}
              ],
              type:{
                  name:'nima'
              }
          }
      });
  </script>
  ```



## 事件

- **on**

```html
<div id="app">
    <button v-on:click="sayHello">click me</button>
</div>

<!--导入vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    let vue = new Vue({
        el: "#app",
        data: {
            type: '男'
        },
        // 方法必须定义在Vue的Methods对象中
        methods: {
            sayHello: function () {
                alert(this.type);
            }
        }
    });
</script>
```























