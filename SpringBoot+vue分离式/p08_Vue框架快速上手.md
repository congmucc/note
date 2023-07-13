[TOC]

## Vue

### - 基本语法

  ```
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
      <!-- 导入vue的链接 -->
      <!-- <script src="../vue.js"></script> -->
      <script src="https://unpkg.com/vue@3"></script>
  </head>
  <body>
      <!-- 2.声明要被vue控制的dom区域 -->
      <div id="app">
          {{message}}
      </div>
      <script>
          // 指定数据源， 即MVVM中的Model
          // const hello = {
          //     data: function() {
          //         return {
          //             message: "hello Vue!"
          //         }
          //     }
          // }
          // const app = Vue.createApp(hello)
          // app.mount('#app')
          
  
  
          Vue.createApp({
              data() {
                  return {
                      message: "hello world"
                  }
              }
          }).mount('#app')
      </script>
  </body>
  </html>
  ```

### - 02内容渲染指令：

  ```
          <!-- 如果想要直接内容被渲染，在属性中加入v-html指令就行 -->
          <p v-html ="desc"></p>
  ```

### - 属性绑定指令：

  ```
          <!-- 渲染到属性要在想要改变的属性前加个 : 指令就行  或者 v-bind:href="link"   主要也是数据绑定-->
          <a :href="link">百度</a>
  ```

### - 使用JavaScript表达式

  ```
          <p>{{number + 1}}</p>
          <p>{{ok ? 'True' : 'False'}}</p>
          <p>{{message.split('').reverse().join('')}}</p>
          <p :id="'list-' + id">xxx</p>
          <p>{{user.name}}</p>
  ```

### - 事件绑定指令:

  ```
  
          <!-- 事件绑定， v-on:  和 @  是等价的-->
  
          <h3>count 的值为 ： {{count}}</h3>
          <button v-on:click="addCount">+1</button>
          <button @click="count+=1">+1</button>
  ```

### - 条件渲染指定:

  ```
          <!-- v-if 如果值是false标签不会被创建， v-show是能被创建，但是会被隐藏，
              v-if 与 v-else 一起使用
              v-show用于频繁的被切换，效率较高 -->
          <button @click="flag = !flag">Toggle Flag</button>
          <p v-if="flag">请求成功，被 v-if 控制</p>
          <p v-show="flag">请求成功，被 v-show 控制</p>
  ```

### - v-else和v-else-if:

  ```
          <p v-if="num > 0.5">随机数大于0.5</p>
          <p v-else>随机数小于0.5</p>
          <hr/>
          <p v-if="type === 'A'">优秀</p>
          <p v-else-if="type === 'B'">良好</p>
          <p v-else-if="type === 'C'">一般</p>
          <p v-else>差</p>
  ```

### - 列表渲染指令:

  ```
              <!-- 跟python有点像， 第一个是代表列表元素，第二个是索引 -->
              <li v-for="(user, i) in userList">索引是: {{i}}, 姓名是：{{user.name}}</li>
              <li v-for="user in userList">姓名是：{{user.name}}</li>
  ```

### - v-for中的key:

  ```
              <!-- 添加用户区域 -->
              <div>
                  <!-- v-model 双向绑定， :value 是单向绑定，即name发生变化 页面会变，但是反过来就不行了 -->
                  <input type="text" v-model="name">
                  <button @click="addNewUser">添加</button>
              </div>
              <!-- 用户列表区域 -->
              <!-- 一般来说，这个key设置的是数据库的id -->
              <li v-for="(user, index) in userList" :key="index">
                  <input type="checkbox" />
                  姓名：{{user.name}}
              </li>
  ```

  > 一般来说，这个key设置的是数据库的id , 防止一些错误, 视频上p9的7分钟