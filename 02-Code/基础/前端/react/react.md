1. 严格模式`<React.StrictMode></React.StrictMode>`会导致api请求发送两次。
2. `React`工作原理和`Vue`的工作原理不一样，`React`依靠模板调用函数更新页面，依靠不断的调用函数来进行更新页面。
3. `React`如果像重新调用组件函数（触发页面更新（渲染））需要`props`或者`state`发生改变。
4. 