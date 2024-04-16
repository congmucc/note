
## 1 闭包
### 1.1 概念：

**闭包函数**：闭包函数声明在一个函数中的函数，叫做闭包函数。 

[闭包](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)：闭包让开发者可以**从内部函数访问外部函数的作用域**。在 JavaScript 中，闭包会随着函数的创建而被同时创建。


```js
function outerFunction(outerVariable) {
  function innerFunction() {
    console.log(outerVariable);
  }
  return innerFunction;
}
 
var inner = outerFunction('Hello Closure');
inner(); // 输出 'Hello Closure'
```

> 在这个例子中，`outerFunction`是一个外部函数，接受一个参数`outerVariable`。它包含一个内部函数`innerFunction`，这个内部函数没有自己的参数或局部变量，但却引用了外部函数的变量`outerVariable`。所以，我们说`innerFunction`是一个闭包，而`outerVariable`就是它的自由变量。

> 需要注意的是，由于JavaScript的垃圾回收机制，如果一个变量离开了它的作用域，那么这个变量就会被回收。但是，由于`innerFunction`是一个闭包，它引用了`outerVariable`，所以即使`outerFunction`执行完毕，`outerVariable`离开了它的作用域，但仍然不会被垃圾回收机制回收。




### 1.2 闭包的作用


### 1.3 闭包的场景

> 一个Ajax请求的成功回调，一个事件绑定的回调方法，一个setTimeout的延时回调，或者一个函数内部返回另一个匿名函数


### 1.4 扩展
> 关键词：词法作用域  
> 加分项：执行上下文机制 V8垃圾回收机制