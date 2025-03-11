# react基础

1. 严格模式`<React.StrictMode></React.StrictMode>`会导致api请求发送两次。
2. `React`工作原理和`Vue`的工作原理不一样，`React`依靠模板调用函数更新页面，依靠不断的调用函数来进行更新页面。
3. `React`如果像重新调用组件函数（触发页面更新（渲染））需要`props`或者`state快照`发生改变。
4. [响应事件 – React 中文文档](https://zh-hans.react.dev/learn/responding-to-events#stopping-propagation)
   - 事件会向上传播。通过事件的第一个参数调用 `e.stopPropagation()` 来防止这种情况。
   - 事件可能具有不需要的浏览器默认行为。调用 `e.preventDefault()` 来阻止这种情况。
   - `<button onClick={handleClick()}/>`这里调用的话不应该加`()`，加了之后就是在渲染过程中 _调用_ 了 `handleClick` 函数。
5. `Hooks` 只能在组件函数的顶层调用 [State：组件的记忆 – React 中文文档](https://zh-hans.react.dev/learn/state-a-components-memory#meet-your-first-hook)
6. Effect称之为副作用（[副作用：（不符合）预期的后果](https://zh-hans.react.dev/learn/keeping-components-pure#side-effects-unintended-consequences)），函数组件的主要目的，是为了渲染生成html元素除了这个主要功能以外，管理状态，fetch数据.等等之外的功能，都可以称之为副作用。useXxx打头的一系列方法，都是为副作用而生的，在react中把它们称为Hooks
7. 对于useEffect()
   在真正渲染html之前会执行它。
   - 没有依赖项，代表每次执行组件函数时都会执行副作用函数。
   - [], 代表副作用函数只会执行一次
   - [依赖项]，依赖项变化时，副作用函数会执行

```js
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId, serverUrl]); // 依赖项为[roomId]
```
8. `useContext` 是一个 React Hook，可以让你读取和订阅组件中的 [context](https://zh-hans.react.dev/learn/passing-data-deeply-with-context)。
   主要作用是为深层组件传递值。
   `ThemeContext.Provider`的值`value`是一个设置上下文值得标签
```js
import { createContext, useContext } from 'react';

const ThemeContext = createContext(null);

export default function MyApp() {
  return (
    <ThemeContext.Provider value="dark">
      <Panel title="Welcome">
        <Button>Sign up</Button>
        <Button>Log in</Button>
      </Panel>
    </ThemeContext.Provider>
  )
}

function Panel({ title, children }) {
  const theme = useContext(ThemeContext);
  {children}
}

function Button({ children }) {
  const theme = useContext(ThemeContext);
  {children}
}

```




用法：如果是使用变量定义一个对象的key的情况需要`[变量]: value`格式。



# redux



# mobx



# router
