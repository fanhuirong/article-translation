> # TypeScript Generics for People Who Gave Up on Understanding Generics
>
> 原文链接： <https://ts.chibicode.com/generics/>

> 作者的话：
> 我的教程是为了帮助初级程序员学习TypeScript，可能对有经验的程序员不那么有用。
>
> 为什么要针对初级程序员？随着 TypeScript 的普及，越来越多的初级程序员将学习和使用它。然而，我注意到许多现有的教程对初级程序员并不那么友好，因此，我尝试着写一个更友好的学习教程。

### 1. TypeScript的泛型太难了？

```ts
// Confused by generics code like this?
function makePair<
    F extends number | string,
    S extends boolean | F
>()
```

你是否对上述这样的泛型代码感到困惑？

如果你
（1）刚接触TypeScript，
（2）刚接触**泛型**，
（3）正在努力理解泛型，那么你就和13年前刚刚学习Java时的我一模一样。

与TypeScript一样，Java编程语言也支持泛型。当我在大学学习Java时，我是一个初级程序员，泛型对我来说感觉非常困难。所以我当时放弃了对泛型的理解，在不知道自己在做什么的情况下就使用了它们。直到大学毕业后找到一份全职工作，我才理解了泛型。

也许你和13年前的我一样，觉得TypeScript泛型太难了。如果是这样，这个教程就是为你准备的! 我将尝试帮助你去真正地理解泛型。

### 2. `makeState()`

首先，我在下面创建了一个名为`makeState()`的函数。我们将使用这个函数来讨论泛型。

```ts
function makeState() {
    let state: number
    function getState() {
        return state
    }
    function setState(x: number) {
        state = x
    }
    return { getState, setState }
}
```

当你运行 `makeState()` 时，它返回两个函数：`getState()` 和 `setState()`。你可以使用这些函数来设置和获取名为state的变量。

让我们来试试吧! 当你运行下面的代码时，什么会被打印到控制台？先试着猜一下，然后按下运行按钮. **（注：请进入原文进行操作）**

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/543f212155324efdb2185412901fe9eb~tplv-k3u1fbpfcp-watermark.image?)

它打印了1，然后是2。很简单，对吗？

**侧面说明**

如果你使用过React，你可能已经意识到`makeState()`与`useState()`钩子函数相似。

你感到困惑吗？有些人可能会疑惑。"为什么我们在另一个函数里面有函数？"或者"`{ getState, setState }`的语法是什么？"如果是这样，请点击这里获得更多解释。

* 上面的代码在函数`makeState()`里面有函数`getState(), setState()`。这是因为我使用了`闭包`，它是JavaScript中最重要的概念之一。如果你没有听说过闭包，我强烈建议你去谷歌一下。这里有一篇[MDN的文章](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)。
* `{ getState, setState }` 语法是JavaScript的ES2015特性（速记属性名称和对象重构 [shorthand property names](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer) 和 [object destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)）。如果你没有见过，我建议你学习ES2015的语法--这里有一个很好的[资料](https://github.com/DrkSephy/es6-cheatsheet)。

### 3. 如果我们使用一个字符串呢？

现在，如果我们不使用1或2这样的数字，而是使用`'foo'`这样的字符串，会发生什么？先试着猜一下，然后按下编译按钮。 **（注：请进入原文进行操作）**

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ad0ba992e0b4b1a9e3eb8999912d976~tplv-k3u1fbpfcp-watermark.image?)

编译失败了，因为`setState()`需要传入一个数字。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a977acca968b45b6a7597df5c0768688~tplv-k3u1fbpfcp-watermark.image?)

为了解决这个问题，我们可以将`state`和`x`的类型从数字改为字符串。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ec67427d089e4a148a40f79afee2d0ce~tplv-k3u1fbpfcp-watermark.image?)

现在它可以工作了! 按下运行试试吧。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/75fc4c09b1384c90924746ce388e6133~tplv-k3u1fbpfcp-watermark.image?)
