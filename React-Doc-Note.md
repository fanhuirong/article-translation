
## 目录

- [Quick Start](#QuickStart)
- [- Think in React](#Think in React)
- [Describing UI](#Describing-UI)

## QuickStart
1. React 组件名必须大写， HTML 标签必须小写。
2. conditional rendering 
  * `if...else` 
  * `? :`
  * `&&`
3. rendering lists
  * `map`
  * `for`
4. responding to events
  * `onClick={handleClick}`
   !最后没有括号，不要调用事件处理函数，只需要传递即可。
5. updatinng the screen
  * render 相同的组件多次，每个组件都有自己独立的 state
6. hooks
  *  只能在组件的顶层调用，想在循环or条件中使需要提取一个新组件。
7. sharing data
  * lifting state up 从而在组件间共享
### Think in React
1. step1 UI to Component
2. build static version
  * 使用 props 传递数据（**！不要用state** ）
3. find the minimal but complete represenntation of UI state
  * DRY(Don't Repeat Yourself)
  * 可以从其他数据计算而来就可以不用state，eg: `arr.length`
4. identify where your state should live
5. add inverse data flow

* **one-way data flow** 单向数据流

## Describing-UI
### 1. first component
* ui 库: `chakra ui` & `material ui`
* react组件是常规 js 函数，但名称必须首字母 **大写** ，否则无法生效。
### 2. import & export
1. `./xx.js` or `./xx` 都可以生效
2. default vs named exports
  一个文件只能有1个 default dexport，但是可以有多个 named exports


| Syntax  | Export statement   | Import statement  |
|---|---|---|
|  Default | `export default function Button() {}`  | `import Button from './button.js';`  |
|  Named | `export function Button() {}`  |  `import { Button } from './button.js';` |

### 3. writing markup with jsx
*  rules of jsx
  1) return a single root element, 
  `<></>` -- React fragment, 不会在HTML中渲染出标签
  > JSX看起来像HTML，但在底层它被转化为普通的JavaScript对象。你不能让一个函数返回两个对象而不把它们包装成一个数组。这就解释了为什么你也不能在不把它们包装成另一个标签或片段的情况下返回两个JSX标签。

  [Why do multiple JSX tags need to be wrapped?](https://beta.reactjs.org/learn/writing-markup-with-jsx#1-return-a-single-root-element)
  
  2) Close all the tags

  3) 驼峰写法 camelCase eg：`className` 
  > 由于历史原因， `aria-*` 和 `data-*`  还是按照 HTML 中的写法

### 4.curly braces `{}`

### 5. pass props to component

1) React 函数组件只接受一个参数，一个 `props` 对象, `{}` 通过解构拿到需要的参数

2) default value
```js
function Avatar({ person, size = 100 }) {
  // ...
}
```
> 仅当缺少 size 属性或传递 `size={undefined}` 时才使用默认值。但如果传递 `size={null}` 或 `size={0}`，则不会使用默认值。

3) 转发参数
```js
function Profile(props) {
  return (
    <div className="card">
      <Avatar {...props} />
    </div>
  );
}
```

4) JSX as `children`
```js
<Card>
  <Avatar />
</Card>

function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}
```
5)! `immutable`

>`props are immutable`  Props are read-only snapshots in time: every render receives a new version of props.

>当一个组件需要改变它的 `props` 时（例如，为了响应用户的互动或新的数据），它将不得不 "要求 "它的父组件给它传递**不同的 `props` -- 一个新的对象**. 它的旧 `props` 将被丢在一边，最终 `JavaScript` 引擎将收回被它们占用的内存。

6) Don’t try to “change props

>`Don’t try to “change props”.` 当您需要响应用户输入（例如更改所选颜色）时，您将需要 `setState`

### 6. 条件渲染
1）`&&`: 不要在 && 左边放数字，如果左边是0，那么整个表达式就会得到这个值（0），而且React会呈现0而不是什么都没有

### 7. render list
  1)  `map`
  2) `filter`
  3) 箭头函数
  ```js
    // 箭头函数隐含地在=>之后返回表达式，所以你不需要返回语句。
    const listItems = chemists.map(person =>
      <li>...</li> // Implicit return!
    );
    // 如果你的 => 后面有一个{大括号，你必须明确写出return!
    const listItems = chemists.map(person => { // Curly brace
      return <li>...</li>;
    });
  ```
  4) `key`

  生成key的途径：
  * js [crypto.randomUUID()](https://developer.mozilla.org/en-US/docs/Web/API/Crypto/randomUUID)
  * a package like [uuid](https://www.npmjs.com/package/uuid)
  > 短的`<></>`片段语法不会让你传递一个键，所以你需要把它们分组到一个`<div>`中，或者使用稍长和更明确的`<Fragment>`语法。
 5) 为什么需要keys
 * **不要用 index 做 key**。你可能会想用一个项目在数组中的索引作为它的键。事实上，如果你不指定一个键，React会使用这个键。但是，如果一个项目被插入、删除，或者数组被重新排序，你渲染项目的顺序会随着时间的推移而改变。索引作为一个键，往往会导致微妙而混乱的错误。

  * 不要临时产生 key。例如用`key={Math.random()}`。这将导致键值在不同的渲染中永远不匹配，导致你的所有组件和DOM每次都被重新创建。这不仅很慢，而且还会丢失列表项中的任何用户输入。相反，**使用一个基于数据的稳定ID。**

* **组件不会将key作为一个 prop 接收**。它只被React本身作为一个提示使用。如果你的组件需要一个ID，你必须把它作为一个单独的道具来传递。`<Profile key={id} userId={id} />`

### 8. [Keeping Components Pure](https://beta.reactjs.org/learn/keeping-components-pure)

* pure function
1. 做它自己的事。它不改变任何在它被调用之前存在的对象或变量。
2. 相同的输入，相同的输出。在输入相同的情况下，一个纯函数应该总是返回相同的结果。

React假设你写的每个组件都是一个纯函数。这意味着你写的React组件必须总是在相同的输入下返回相同的JSX。

[bad example](https://beta.reactjs.org/learn/keeping-components-pure#side-effects-unintended-consequences)

* strict mode
通过两次调用组件函数，严格模式有助于找到违反这些规则的组件。

虽然函数式编程在很大程度上依赖于纯洁性，但在某些时候，某些地方，有些东西必须改变。这就是编程的意义所在。这些变化--更新屏幕、启动动画、改变数据--被称为**副作用**。它们是发生在 "on the side"的事情，而不是在渲染过程中。

在React中，**side effects**通常属于事件处理程序。事件处理程序是React在你执行某些动作时运行的函数--例如，当你点击一个按钮时。即使事件处理程序被定义在你的组件内，它们也不会在渲染过程中运行！所以事件处理程序**不需要在渲染过程中运行**。所以**事件处理程序不需要是pure的**。

* Why does React care about purity?
>1. 组件可以在不同的环境中运行。由于它们对相同的输入返回相同的结果，一个组件可以为许多用户请求提供服务。
>2. 以通过跳过渲染那些输入没有改变的组件来提高性能。这很安全，因为纯函数总是返回相同的结果，所以它们可以安全地进行缓存。
>3. 如果在渲染深层组件树的过程中，一些数据发生了变化，React可以重新开始渲染，而不需要浪费时间来完成过时的渲染。纯粹性使得它可以在任何时候安全地停止计算。

* 渲染可以在任何时候发生，所以组件不应该依赖彼此的渲染顺序。

* 您不应更改组件用于渲染的任何输入。这包括 props、state 和 context。要更新screen ，set state 而不是改变预先存在的对象。
