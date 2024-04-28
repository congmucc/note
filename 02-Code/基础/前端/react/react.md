1. 严格模式`<React.StrictMode></React.StrictMode>`会导致api请求发送两次。
2. `React`工作原理和`Vue`的工作原理不一样，`React`依靠模板调用函数更新页面，依靠不断的调用函数来进行更新页面。
3. `React`如果像重新调用组件函数（触发页面更新（渲染））需要`props`或者`state快照`发生改变。
4. [响应事件 – React 中文文档](https://zh-hans.react.dev/learn/responding-to-events#stopping-propagation)
   - 事件会向上传播。通过事件的第一个参数调用 `e.stopPropagation()` 来防止这种情况。
   - 事件可能具有不需要的浏览器默认行为。调用 `e.preventDefault()` 来阻止这种情况。
   - `<button onClick={handleClick()}/>`这里调用的话不应该加`()`，加了之后就是在渲染过程中 _调用_ 了 `handleClick` 函数。
5. `Hooks` 只能在组件函数的顶层调用 [State：组件的记忆 – React 中文文档](https://zh-hans.react.dev/learn/state-a-components-memory#meet-your-first-hook)
6. 