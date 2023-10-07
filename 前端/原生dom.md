### $event

#### 场景1：获取原生DOM事件的事件对象

在DOM事件的[回调函数](https://so.csdn.net/so/search?q=回调函数&spm=1001.2101.3001.7020)中传入参数`$event`，可以获取到该事件的事件对象

<template> 
    <button @click="getData($event)">按钮</button> </template> 
<script> export default {    
    setup() {
        const getData = (e) => { 
            console.log(e)
        }        
        return {
            getData 
        } 
    } 
} 
</script>

当我们点击button按钮时，可以看到控制台打印出的事件对象，例如常用的e.target.value



#### 场景2：事件注册所传的参数(子组件向父组件传值)

在子组件中通过`$emit`注册事件，将数据作为参数传入，在父组件中通过`$event`接收

[ Vue中的$event详解_$enent_violateer的博客-CSDN博客](https://blog.csdn.net/violateer/article/details/108900251)