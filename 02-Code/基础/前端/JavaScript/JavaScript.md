
## 1 闭包
### 1.1 概念：

[10分钟带你深入理解JavaScript的执行上下文和闭包机制-CSDN博客](https://blog.csdn.net/qq_48652579/article/details/132848583)

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

> 再者，每次调用外部函数，都会为内部的闭包创建一个新的作用域。例如：

```js
var inner1 = outerFunction('Hello Closure 1');
var inner2 = outerFunction('Hello Closure 2');
inner1(); // 输出 'Hello Closure 1'
inner2(); // 输出 'Hello Closure 2'
```

> 这里，`inner1`和`inner2`是两个不同的闭包。他们分别有自己的作用域，储存了不同的`outerVariable`。

### 1.2 闭包的作用


### 1.3 闭包的场景

> 一个Ajax请求的成功回调，一个事件绑定的回调方法，一个setTimeout的延时回调，或者一个函数内部返回另一个匿名函数

> 具体场景可以看1.1中的CSDN博客


```js
function fetchData(url, callback) {
  fetch(url).then(function (response) {
    return response.json();
  }).then(function (data) {
    callback(data);
  });
}
 
function processData(data) {
  console
 
.log(data);
}
 
fetchData('https://api.example.com/data', processData);
```

> 在这个例子中，`fetchData`函数通过闭包捕获了`processData`函数作为回调函数。当异步操作完成时，它会调用回调函数并传递数据给它。闭包保持了回调函数的上下文，使得回调函数可以访问外部的`processData`函数。


### 1.4 扩展
> 关键词：词法作用域  
> 加分项：执行上下文机制 V8垃圾回收机制