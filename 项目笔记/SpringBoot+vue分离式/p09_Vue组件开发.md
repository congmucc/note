[TOC]

## NPM

- NPM（Node Package Manager）是一个NodeJS包管理和分发工具。 
- NPM以其优秀的依赖管理机制和庞大的用户群体，目前已经发展成为整个JS领 域的依赖管理工具
- NPM最常见的用法就是用于安装和更新依赖。要使用NPM，首先要安装Node 工具。

## Node.js安装

[Node.js (nodejs.org)](https://nodejs.org/zh-cn)

npm install -g @vue/cli

## Vue CLI使用

- Vue CLI是Vue官方提供的构建工具，通常称为**脚手架**。
- 用于快速搭建一个带有热重载（在代码修改后不必刷新页面即可呈现修改后的 效果）及构建生产版本等功能的单页面应用。
- Vue CLI基于 webpack 构建，也可以通过项目内的配置文件进行配置。
- 安装：npm install -g @vue/cli

vue create 项目名字



运行：

在生成的文件夹下面打开终端 输入 `npm run serve`



## 组件化开发 

- 组件（Component）是Vue.js最强大的功能之一。组件可以扩展HTML元素， 封装可重用的代码。 
- Vue的组件系统允许我们使用小型、独立和通常可复用的组件构建大型应用。



## 组件的构成 

- Vue 中规定组件的后缀名是 .vue 
- 每个 .vue 组件都由 3 部分构成，分别是 
  - template，组件的模板结构，可以包含HTML标签及其他的组件 
  - script，组件的 JavaScript 代码 (组件的行为)
  - style，组件的样式 类似css

**App.vue : ** 

```
<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <!-- 3. 将vue主文件包含进来 -->
  <Hello></Hello>
</template>

<script>
// 1. 引用vue文件
import Hello from '../src/components/Hello.vue'

export default {
  name: 'App',
  components: {
    // 2. 进行注册
    Hello
  }
}
</script>
```

> export default 是导出，  import是导入，两者相互对应
