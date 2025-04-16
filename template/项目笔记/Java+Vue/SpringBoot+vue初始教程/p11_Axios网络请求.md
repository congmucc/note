[TOC]

## Axios简介

- 在实际项目开发中，前端页面所需要的数据往往需要从服务器端获取，这必然 涉及与服务器的通信。 
- Axios 是一个基于 promise 网络请求库，作用于node.js 和浏览器中。
- Axios 在浏览器端使用XMLHttpRequests发送网络请求，并能自动完成JSON 数据的转换 。
- 安装：npm install axios 
- 地址：https://www.axios-http.cn/

## 发送网络请求

```
  // 因为这是Vue自带的函数，与data平级，所以不用放在methods中，只有自己创建的函数才能被放在methods中, 这是一个异步的过程，先打印App被挂载
  created:function() {
    console.log("App组件被创建了")
    axios.get("http://localhost:8088/user/findAll").then(function(response){
      console.log(response)
    })

  },
  mounted:function() {
    console.log("App被挂载(渲染)")
  },

```

- 其他请求方式参考：https://axios-http.com/zh/docs/req_config



## 异步回调问题

> 主要解决异步问题

- async/await

  ```
  async function getUser() {
    try {
      const response = awwit.axios.get('/user?ID=13245');
      console.log(response);
    } catch (error) {
      console.error(error)
    }
  }
  ```

## 为什么会出现跨域问题 

- 为了保证浏览器的安全，不同源的客户端脚本在没有明确授权的情况下，不能 读写对方资源，称为同源策略，同源策略是浏览器安全的基石
- 同源策略（Sameoriginpolicy）是一种约定，它是浏览器最核心也最基本的安 全功能
- **所谓同源（即指在同一个域）就是两个页面具有相同的协议（protocol），主 机（host）和端口号（port）**
- 当一个请求url的协议、域名、端口三者之间任意一个与当前页面url不同即为跨 域，此时无法读取非同源网页的 Cookie，无法向非同源地址发送 AJAX 请求

## 跨域问题解决 

- CORS（Cross-Origin Resource Sharing）是由W3C制定的一种跨域资源共享 技术标准，其目的就是为了解决前端的跨域请求。 

- CORS可以在不破坏即有规则的情况下，通过后端服务器实现CORS接口，从而 实现跨域通信。

- CORS将请求分为两类：**简单请求和非简单请求**，分别对跨域通信提供了支持。





## 简单请求 

### 简介：

- 满足以下条件的请求即为简单请求： 

- 请求方法：GET、POST、HEAD n 除了以下的请求头字段之外，没有自定义的请求头： 

  - Accept、Accept-Language、Content-Language、Last-Event-ID、 

- Content-Type n Content-Type的值只有以下三种：
  -  text/plain、multipart/form-data、application/x-www-form-urlencoded

### 处理：

- 对于简单请求，CORS的策略是请求时在请求头中增加一个Origin字段，

  ``` 
  Host:localhost:8080
  Origin: http://localhost:8081
  Referer:http://localhost:8081/index.html
  ```

- Origin 告诉服务器请求来自哪里

- 服务器收到请求后，根据该字段判断是否允许该请求访问，如果允许，则在 HTTP(响应)头信息中添加Access-Control-Allow-Origin字段。

  ```
  Access-Control-Allow-Origin: http://localhost:8081
  Content-Length: 20
  Content-Type: text/plain;charset=UTF-8
  Date: Thu, 12 Jul 2018 12:51:14 GMT
  ```

  



## 非简单请求 

### 简介：

- 对于非简单请求的跨源请求，浏览器会在真实请求发出前增加一次OPTION请 求，称为预检请求（preflight request）

- 预检请求将真实请求的信息，包括请求方法、自定义头字段、源信息添加到 HTTP头信息字段中，询问服务器是否允许这样的操作。 

- 例如一个GET请求： 

  ```
  OPTIONS /test HTTP/1.1
  OOrigin: http://www.test.com
  Access-Control-Request-Method: GET
  Access- Control-Request-Headers: X-Custom-Header
  Host: www.test.com
  ```

- Access-Control-Request-Method表示请求使用的HTTP方法

- Access- Control-Request-Headers包含请求的自定义头字

### 处理：

- 服务器收到请求时，需要分别对Origin、Access-Control-Request-Method、 Access-Control-Request-Headers进行验证，验证通过后，会在返回HTTP头 信息中添加： 

  ```
  Access-Control-Request-Origin:http://www.test.com
  Access-Control-Allow-Methods: GET, POST, PUT
  Access-Control-Allow-Headers: X-Custom-Header
  Access-Control-Allow-Credentials: true
  Access-Control-Max-Age: 1278000
  ```

- Access-Control-Allow-Methods、Access-Control-Allow-Headers：真实请求允许的方法、允许使用的字段 

- Access-Control-Allow-Credentials: 是否允许用户发送、处理cookie

- Access-Control-Max-Age: 预检请求的有效期，单位为秒，有效期内不会重复 发送预检请求。

- 当预检请求通过后，浏览器才会发送真实请求到服务器。这样就实现了跨域资 源的请求访问。

## Spring Boot中配置CORS 

- 在传统的Java EE开发中，可以通过过滤器统一配置，而Spring Boot中对此则 提供了更加简洁的解决方案

```
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class CorsConfig implements WebMvcConfigurer {
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")  //允许跨域访问的路径
                .allowedOrigins("*")  // 允许跨域访问的源
                .allowedMethods("POST", "GET", "PUT", "OPTIONS", "DELETE") // 允许请求方法
                .maxAge(168000)  // 预检间隔时间
                .allowedHeaders("*")  // 允许头部设置
                .allowCredentials(true); // 是否发送cookie
    }
}

```

> 这是一个全局配置

- **或者控制器(Controller)中加个注解@CrossOrigin也行(这个不是全局的，仅仅加的控制器里面才行)**

## 与Vue整合

- 在实际项目开发中，几乎每个组件中都会用到 axios 发起数据请求。此时会遇 到如下两个问题： 
  - 每个组件中都需要导入 axios
  - 每次发请求都需要填写完整的请求路径
  
- 可以通过全局配置的方式解决上述问题：

  ```
  // 全局导入axios  this.$http 这个作为axios代替
  import axios from 'axios';
  
  // 配置请求根路径
  axios.defaults.baseURL = "http://localhost8080"
  
  // 将axios作为全局的自定义属性， 每个组件可以在内部直接访问(Vue2)
  Vue.prototype.$http = axios
  
  // 将axios作为全局的自定义属性， 每个组件可以在内部直接访问(Vue3)
  // App.config.globalProperties.$http = axios
  ```

  > 在组件中不用axios.get()，用this.$http.get()，这个http是自己定义的，可看下面的回调函数中的this.$http代码示例

- 关于回调函数（数据传参会遇到）

  ```
        // 因为这是Vue自带的函数，与data平级，所以不用放在methods中，只有自己创建的函数才能被放在methods中, 这是一个异步的过程，先打印App被挂载
      created:function() {
          console.log("App组件被创建了")
          // 回调函数里面的this会发生变化，这个箭头(箭头函数)是让this的作用域继承父级，即与created的this一样的
          // axios.get("http://localhost:8088/user/findAll").then(function(response){
  
          // axios.get("http://localhost:8088/user/findAll").then((response)=>{  这个是单个的导入axios， this.$http 是全局状态的axios
          this.$http.get("http://localhost:8088/user/findAll").then((response)=>{
              this.tableData = response.data
          console.log(response.data)
          })
      },
  ```

  > 遇见这个问题主要是前端请求数据，后端传过来数据库的数据，然后前端将数据进行赋值的时候会遇见，this这个会有作用域问题