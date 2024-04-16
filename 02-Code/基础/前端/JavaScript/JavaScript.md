
## 1 闭包
### 1.1 概念：

**闭包函数**：闭包函数声明在一个函数中的函数，叫做闭包函数。 

[闭包](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)：闭包（closure）是一个函数以及其捆绑的周边环境状态（lexical environment，词法环境）的引用的组合。换而言之，闭包让开发者可以从内部函数访问外部函数的作用域。在 JavaScript 中，闭包会随着函数的创建而被同时创建。


```js
function init() {
  var name = "Mozilla"; // name 是一个被 init 创建的局部变量
  function displayName() {
    // displayName() 是内部函数，一个闭包
    alert(name); // 使用了父函数中声明的变量
  }
  displayName();
}
init();

```






