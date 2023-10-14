[TOC]

## 组件间的传值 

- 组件可以由内部的Data提供数据， 也可以由父组件通过prop的方式传值

  ```
  <script>
      // 导出， 这个export default 与外面的import相对应, {}里面可以加主键的属性
  export default {
      name:"Move", // 一般来说用不上
      // 自定义的属性， 定义之后div里面就可以使用这个props里面定义的数据
      props:["title", "rating"],
      data:function() {
          return {
              
          }
      },
      methods: {
  
      }
  }
  </script>
  ```

  - App.vue中

  ```
  <template>
    <div id="app">
      <!-- 加 : 进行数据绑定 -->
      <Movie v-for="movie in movies" :key="movie.id" :title="movie.title" :rating="movie.rating"></Movie>
    </div>
  </template>
  
  <script>
  import Movie from './components/Movie.vue';
  
  export default {
    name: 'App',
    data:function() {
      return {
        // 数组
        movies:[
          {id:1, title:"金刚狼1",rating:8.7},
          {id:2, title:"金刚狼2",rating:8.8},
          {id:3, title:"金刚狼3",rating:8.9}
        ]
      }
    },
    components: {
      Movie
    }
  }
  </script>
  ```

  



- 兄弟组件之间可以通过Vuex等统一数据源提供数据共享。





## element-ui介绍 

- Element是国内饿了么公司提供的一套开源前端框架，简洁优雅，提供了Vue、 React、Angular等多个版本。 

- 文档地址：https://element.eleme.cn/#/zh-CN/ 

- 安装：`npm i element-ui` 

  > 一般也会有`npm i element-ui -S` 加个-S的意思是将element-ui 记录到package.json当中去，不写也可以记录进去

- 引入 Element：

  > 需要下载的依赖会下载到node_modules中，步骤是在终端上的文件夹下输入安装的指令就行

  

- 引用别人的代码没有依赖怎么办

  **终端输入 `npm install`**
  
  > 原理： 1. 所有的依赖都会被记录在package.json里面的dependencies, 所以说可以在线联网下载，并且文件夹也可以直接下载

## 第三方图标库 

- 由于Element UI提供的字体图符较少，一般会采用其他图表库，如著名的Font Awesome 
- Font Awesome提供了675个可缩放的矢量图标，可以使用CSS所提供的所有特 性对它们进行更改，包括大小、颜色、阴影或者其他任何支持的效果。 
- 文档地址：http://fontawesome.dashgame.com/ 
- 安装：npm install font-awesome 
- 使用：import 'font-awesome/css/font-awesome.min.css'



- 一般一个<template></template>中只能有一个div，多个的话，放在一个里面就行