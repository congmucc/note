## 脚手架

### 项目入口将会进入main.js

```
/*
   该文件时整个项目的入口文件

*/

// 引入Vue
import Vue from 'vue'
// 引入App组件，它是所有组件的父组件
import App from './App.vue'

// 关闭vue的生产提示
Vue.config.productionTip = false

// 创建Vue实例对象---vm
new Vue({
  // 将App组件放入容器中
  render: h => h(App),
}).$mount('#app')
// id = app的容器在public/index.html中
```

### 引入vue

### 引入App组件

```
<template>
  <div id="app">
    <School></School>
    <Student></Student>
  </div>
</template>

<script>

import School from './components/School.vue';
import Student from './components/Student.vue';

export default {
  name: "App",
  components: {
    School,
    Student
  },
};
</script>

<style>
</style>
```



