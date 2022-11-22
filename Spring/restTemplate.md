# RestTemplate 用法详解

## RestTemplate 简介

RestTemplate是从spring3.0开始支持的一个Http工具，它提供了常见的REST请求方案的模板，例如GET请求、POST的请求、PUT请求、DELETE请求以及一些通用的请求执行方法 exchange 以及 execute。RestTemplate 继承自 InterceptingHttpAccessor 并且实现了 RestOperations 接口，其中 RestOperations 接口定义了基本的 RESTful 操作，这些操作在 RestTemplate 中都得到了实现。接下来我们就来看看这些操作方法的使用。



## 用法实战

在开始下面的案例之前，我们需要先创建一个工程，命名为 RestTemplate 。和上篇文章的项目结构一样，在 RestTemplate 中，我们也分别创建子项目 eureka 、provider 以及 consumer ，将 provider 和 consumer 分别注册到 eureka 上面去。这个具体的步骤大家可以参考上篇文章，本文我就不赘述了，这是我们的准备工作。



## GET 请求

做好了准备工作，先来看使用 RestTemplate 发送 GET 请求。在 RestTemplate 中，和 GET 请求相关的方法有如下几个：

![](https://img.mukewang.com/5d00dbf10001676611540246.png)

这里的方法一共有两类，getForEntity 和 getForObject，每一类有三个重载方法，下面我们分别予以介绍。



### getForEntity

既然 RestTemplate 发送的是 HTTP 请求，那么在响应的数据中必然也有响应头，如果开发者需要获取响应头的话，那么就需要使用 getForEntity 来发送 HTTP 请求，此时返回的对象是一个 ResponseEntity 的实例。这个实例中包含了响应数据以及响应头。

例如，在 provider 中提供一个 HelloController 接口，HelloController 接口中定义一个 sayHello 的方法，如下：

```java
@RestController
public class HelloController {
    @GetMapping("/hello")
    public String sayHello(String name) {
        return "hello " + name + " !";
    }
}
```

在 consumer 中定义一个 UseHelloController 的类，再定义一个 `/hello` 接口，在接口中调用 provider 提供的服务，如下：

```java
@RestController
public class UseHelloController {
    @Autowired
    DiscoveryClient discoveryClient;
    @Autowired
    RestTemplate restTemplate;

    @GetMapping("/hello")
    public String hello(String name) {
        List<ServiceInstance> list = discoveryClient.getInstances("provider");
        ServiceInstance instance = list.get(0);
        String host = instance.getHost();
        int port = instance.getPort();
        String url = "http://" + host + ":" + port + "/hello?name={1}";
        ResponseEntity<String> responseEntity = restTemplate.getForEntity(url, String.class, name);
        StringBuffer sb = new StringBuffer();
        HttpStatus statusCode = responseEntity.getStatusCode();
        String body = responseEntity.getBody();
        sb.append("statusCode：")
                .append(statusCode)
                .append("</br>")
                .append("body：")
                .append(body)
                .append("</br>");
        HttpHeaders headers = responseEntity.getHeaders();
        Set<String> keySet = headers.keySet();
        for (String s : keySet) {
            sb.append(s)
                    .append(":")
                    .append(headers.get(s))
                    .append("</br>");
        }
        return sb.toString();
    }
}

```

关于 **DiscoveryClient **那一段本文先不做讨论，主要来看 **getForEntity **方法。第一个参数是**url**，**url**中有一个占位符**{1}** ,如果有多个占位符分别用 **{2} 、 {3} **… 去表示，第二个参数是接口返回的数据类型，最后是一个可变长度的参数，用来给占位符填值。在返回的 **ResponseEntity** 中，可以获取响应头中的信息，其中 **getStatusCode** 方法用来获取响应状态码， **getBody** 方法用来获取响应数据， **getHeaders** 方法用来获取响应头，在浏览器中访问该接口，结果如下：

![](https://img.mukewang.com/5d00dbfc000166bf09240302.png)

当然，这里参数的传递除了这一种方式之外，还有另外两种方式，也就是 getForEntity 方法的另外两个重载方法。

第一个是占位符不使用数字，而是使用参数的 key，同时将参数放入到一个 map 中。map 中的 key 和占位符的 key 相对应，map 中的 value 就是参数的具体值，例如还是上面的请求，利用 map 来传递参数，请求方式如下：

```java
Map<String, Object> map = new HashMap<>();
String url = "http://" + host + ":" + port + "/hello?name={name}";
map.put("name", name);
ResponseEntity<String> responseEntity = restTemplate.getForEntity(url, String.class, map);
```



这种方式传参可能看起来更直观一些。

第二个是使用 Uri 对象，使用 Uri 对象时，参数可以直接拼接在地址中，例如下面这样：

```java
String url = "http://" + host + ":" + port + "/hello?name="+ URLEncoder.encode(name,"UTF-8");
URI uri = URI.create(url);
ResponseEntity<String> responseEntity = restTemplate.getForEntity(uri, String.class);
```

但需要**注意**的是，这种传参方式，参数如果是中文的话，需要对参数进行编码，使用 `URLEncoder.encode` 方法来实现。



### getForObject

getForObject 方法和 getForEntity 方法类似，getForObject 方法也有三个重载的方法，参数和 getForEntity 一样，因此这里我就不重复介绍参数了，这里主要说下 getForObject 和 getForEntity 的差异,这两个的差异主要体现在返回值的差异上，

- getForObject 的返回值就是服务提供者返回的数据，使用 getForObject 无法获取到响应头,例如，还是上面的请求，利用 getForObject 来发送 HTTP 请求，结果如下：

```java
String url = "http://" + host + ":" + port + "/hello?name=" + URLEncoder.encode(name, "UTF-8");
URI uri = URI.create(url);
String s = restTemplate.getForObject(uri, String.class);
```

注意，这里返回的 s 就是 provider 的返回值，如果开发者只关心 provider 的返回值，并不关系 HTTP 请求的响应头，那么可以使用该方法。



## POST 请求

和 GET 请求相比，RestTemplate 中的 POST 请求多了一个类型的方法，如下：

![](https://img.mukewang.com/5d00dc050001e88012780366.png)

可以看到，post 请求的方法类型除了 postForEntity 和 postForObject 之外，还有一个 postForLocation。这里的方法类型虽然有三种，但是这三种方法重载的参数基本是一样的，因此这里我还是以 postForEntity 方法为例，来剖析三个重载方法的用法，最后再重点说下 postForLocation 方法。



### postForEntity

在 POST 请求中，参数的传递可以是 key/value 的形式，也可以是 JSON 数据，分别来看：

1. 传递 key/value 形式的参数

首先在 provider 的 HelloController 类中再添加一个 POST 请求的接口，如下：

```java
@PostMapping("/hello2")
public String sayHello2(String name) {
    return "Hello " + name + " !";
}
```

然后在 consumer 中添加相应的方法去访问，如下：

```java
@GetMapping("/hello5")
public String hello5(String name) {
    List<ServiceInstance> list = discoveryClient.getInstances("provider");
    ServiceInstance instance = list.get(0);
    String host = instance.getHost();
    int port = instance.getPort();
    String url = "http://" + host + ":" + port + "/hello2";
    MultiValueMap map = new LinkedMultiValueMap();
    map.add("name", name);
    ResponseEntity<String> responseEntity = restTemplate.postForEntity(url, map, String.class);
    return responseEntity.getBody();
}
```

在这里， postForEntity 方法第一个参数是请求地址，第二个参数 map 对象中存放着请求参数 key/value，第三个参数则是返回的数据类型。当然这里的第一个参数 url 地址也可以换成一个 Uri 对象，效果是一样的。这种方式传递的参数是以 key/value 形式传递的，在 post 请求中，也可以按照 get 请求的方式去传递 key/value 形式的参数，传递方式和 get 请求的传参方式基本一致，例如下面这样：

```java
@GetMapping("/hello6")
public String hello6(String name) {
    List<ServiceInstance> list = discoveryClient.getInstances("provider");
    ServiceInstance instance = list.get(0);
    String host = instance.getHost();
    int port = instance.getPort();
    String url = "http://" + host + ":" + port + "/hello2?name={1}";
    ResponseEntity<String> responseEntity = restTemplate.postForEntity(url, null, String.class,name);
    return responseEntity.getBody();
}
```

此时第二个参数可以直接传一个 null。



2. 传递 JSON 数据

上面介绍的是 post 请求传递 key/value 形式的参数，post 请求也可以直接传递 json 数据，在 post 请求中，可以自动将一个对象转换成 json 进行传输，数据到达 provider 之后，再被转换为一个对象。具体操作步骤如下：

首先在 RestTemplate 项目中创建一个新的maven项目，叫做 commons ，然后在 commons 中创建一个 User 对象，如下：

```java
public class User {
    private String username;
    private String address;
    //省略getter/setter
}
```

然后分别在 provider 和 consumer 的 pom.xml 文件中添加对 commons 模块的依赖，如下：

```pom
<dependency>
    <groupId>com.justdojava</groupId>
    <artifactId>commons</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```

这样，在 provider 和 consumer 中就都能使用 User 对象了。首先在 provider 中创建一个添加用户的接口，如下：

```java
@Controller
@ResponseBody
public class UserController {
    @PostMapping("/user")
    public User hello(@RequestBody User user) {
        return user;
    }
}
```

这里的接口很简单，只需要将用户传来的 User 对象再原封不动地返回去就行了，然后在 consumer 中添加一个接口来测试这个接口，如下：

```java
@GetMapping("/hello7")
public User hello7() {
    List<ServiceInstance> list = discoveryClient.getInstances("provider");
    ServiceInstance instance = list.get(0);
    String host = instance.getHost();
    int port = instance.getPort();
    String url = "http://" + host + ":" + port + "/user";
    User u1 = new User();
    u1.setUsername("牧码小子");
    u1.setAddress("深圳");
    ResponseEntity<User> responseEntity = restTemplate.postForEntity(url, u1, User.class);
    return responseEntity.getBody();
}
```

看到这段代码有人要问了，这不和前面的一样吗？是的，唯一的区别就是第二个参数的类型不同，这个参数如果是一个 MultiValueMap 的实例，则以 key/value 的形式发送，如果是一个普通对象，则会被转成 json 发送。

### postForObject

postForObject 和 postForEntity 基本一致，就是返回类型不同而已，这里不再赘述。

postForLocation 方法的返回值是一个 Uri 对象，因为 POST 请求一般用来添加数据，有的时候需要将刚刚添加成功的数据的 URL 返回来，此时就可以使用这个方法，一个常见的使用场景如用户注册功能，用户注册成功之后，可能就自动跳转到登录页面了，此时就可以使用该方法。例如在 provider 中提供一个用户注册接口，再提供一个用户登录接口，如下：