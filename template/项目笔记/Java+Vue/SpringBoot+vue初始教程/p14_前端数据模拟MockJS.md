[TOC]

## Mockjs介绍 

- Mock.js 是一款前端开发中拦截Ajax请求再生成随机数据响应的工具，可以用 来模拟服务器响应. 、

- 优点是非常简单方便, 无侵入性, 基本覆盖常用的接口数据类型.。
-  支持生成随机的文本、数字、布尔值、日期、邮箱、链接、图片、颜色等。 
- 安装：npm install mockjs

> 其实就是一个模拟的接口，后端没有写好，无法返回数据，这时候就用mockjs进行模拟数据测试

## 基本使用 

### 1.  在项目中创建mock目录，新建index.js文件

```
// 引入mockjs
import Mock from "mockjs";
// 设置 延迟时间
Mock.setup({
    timeout: 4000
})

// 使用mockjs模拟数据
Mock.mock('/product/search', {
    // 键值对
    "ret":0,
    "data":
    {
        "mtime": "@datetime", // 随机生成日期时间
        "score|1-800": 1, // 随机生成1-800的数字
        "rank|1-100" : 1, // 随机生成1-100的数字
        "stars|1-5": 1, // 随机生成1-5的数字
        "nickname" : "@cname", // 随机生成中文名字
        // 生成图片
        "img" : "@image('200x100', '#ffcc33', '#fff', 'png', 'Fast Mock')"
    }
})
```

- Mock.mock( rurl?, rtype?, template|function( options ) )
-  rurl，表示需要拦截的 URL，可以是 URL 字符串或 URL 正则
-  rtype，表示需要拦截的 Ajax 请求类型。例如 GET、POST、PUT、DELETE 等。
- template，表示数据模板，可以是对象或字符串 
- function，表示用于生成响应数据的函数。
- 设置延时请求到数据





然后在main.js里面导入

```
import './mock'

// 或者   index.js  会被自动导入，这两个那个都可
import './mock/index.js'
```



在组件中导入axios进行模拟

```
<script>
// 导入axios
import axios from 'axios'
export default {
  name: 'App',
  components: {
  },
  // 被渲染的时候加载
  mounted:function() {
    // 发送get请求  里面的url要于mock.js中的mock函数的一样
    axios.get('/product/search').then(res => {
      // 这里面有两层data，  因为res中也有一个data，存储返回的数据，
      //mock.js发送的data是在res.data中
      console.log(res.data.data.img)
    })
  }
}
</script>
```



```
<template>
  <div id="app">
    <img alt="Vue logo" :src="img">
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'App',
  data:function(){
    return {
      img:""
    }
  },
  // 被渲染的时候加载
  mounted:function(){
    axios.get('/product/search').then(res => {
      // 这里面有两层data，  因为res中也有一个data，存储返回的数据，
      //mock.js发送的data是在res.data中
      console.log(res.data.data.img)
      this.img = res.data.data.img
    })
  }
}
</script>

```

### 2. mock生成数据语法规则

看视频14分钟后



```
当axios.get('/product/search')为当axios.get('/product/search?id=10')这个情况的时候
也就是不匹配

可以将Mock.mock('product/search', {

})
写为
Mock.mock(RegExp('product/search.*'), {

})
```



### 3. 组件中调用mock.js中模拟的数据接口，这时返回的response就是mock.js中用 Mock.mock(‘url’,data）中设置的data