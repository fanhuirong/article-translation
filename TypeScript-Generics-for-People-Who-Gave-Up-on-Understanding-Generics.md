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

**附注**

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

### 4. 挑战：两个不同的state状态

现在，我们已经掌握了基本知识，这里有一个给你的挑战。

问题：我们能否修改`makeState()`，使其能够创建两种不同的状态：一种只允许接收数字，另一种只允许接收字符串？

这就是我的意思：
![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8416f4aaaaf84a1ca72b4d28c69e1095~tplv-k3u1fbpfcp-watermark.image?)

在前面，我们的第一个`makeState()`创建了纯数字的状态，第二个`makeState()`创建了纯字符串的状态。然而，它不能同时创建纯数字的状态和纯字符串的状态。

我们如何修改`makeState()`来实现我们的目标？

### 5. 尝试1

这里是第一种尝试。它能正常工作吗？

```ts
function makeState() {
    let state: number | string
    function getState() {
        return state
    }
    function setState(x: number | string) {
        state = x
    }
    return { getState, setState }
}
```

**这样做是行不通的。**
你最终会创建一个**同时允许数字和字符串**的状态，这不是我们想要的。相反，我们希望`makeState()`支持创建两种不同的状态：**一种是只允许数字，另一种是只允许字符串**。

### 6. 尝试2: 泛型

这就是泛型的作用，请看下面的例子。

```ts
function makeState<S>() {
  let state: S
  function getState() {
    return state
  }
  function setState(x: S) {
    state = x
  }
  return { getState, setState }
}
```

`makeState()` 现在被定义为 `makeState<S>()`。你可以把`<S>`看作是你在调用该函数时必须传入的另一个东西。**但你不是传递一个值，而是传递一个类型给它。**

例如，当你调用`makeState()`时，你可以把类型数字作为`S`传递。

```ts
// It sets S as number
makeState<number>()
```

然后，在`makeState()`的函数定义里面，`S`将变成数字类型。

```ts
// In the function definition of makeState()
let state: S // <- number
function setState(x: S /* <- number */) {
    state = x
}
```

因为`state`将是数字类型，而`setState`只接受数字，所以它创建了一个纯数字的状态。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f1c8acf85a0547c7b10058862e6145d4~tplv-k3u1fbpfcp-watermark.image?)

另一方面，为了创建一个纯字符串的`state`，你可以在调用`makeState()`时将字符串作为`S`传递。

注意：我们称 `makeState<S>()` 为 "**泛型函数**"，因为它实际上是泛型的--你可以选择让它只包含数字或只包含字符串。如果它接受一个类型参数，你就知道它是一个通用函数。

**附注**

你可能想知道。为什么我们将类型参数命名为 "S"？

答案是。它实际上可以是任何名字，但通常人们使用一个词的第一个字母来描述该类型所代表的内容。在这种情况下，我选择了 "`S`"，因为它描述的是 "`S` "状态的类型。以下名称也很常见。

* `T`（代表 "T "类型）
* `E` (代表 "E "lement)
* `K` (代表 "K "ey)
* `V` (代表 "V "alue)

### 7. 问题：你可以创建一个布尔类型的`state`

但是等一下，如果你把`boolean`传给`S`，你就可以创建一个只有`boolean`的状态。

```ts
// Creates a boolean-only state
const boolState = makeState<boolean>()
boolState.setState(true)
console.log(boolState.getState())
```

假设我们不希望`makeState()`能够创建非数字或非字符串的`state`（如布尔值）。我们怎样才能确保这一点呢？

解决办法。当你声明`makeState()`时，你把类型参数`<S>`改为`<S extends number|string>`。这是你唯一需要做的改变。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/77d77b7150bd46bc8c8a8a242d7360d2~tplv-k3u1fbpfcp-watermark.image?)

通过这样做，当你调用`makeState()`时，你只能将数字、字符串或任何其他扩展了数字或字符串的类型作为`S`传递。

让我们看看当你试图将布尔值作为`S`传递时会发生什么。 **（注：请进入原文进行操作）**

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f0165a02f5c2423cb8b00f43233f9ce8~tplv-k3u1fbpfcp-watermark.image?)

它产生了一个错误，而这正是我们想要的。我们已经成功地阻止了`makeState()`创建非数字或非字符串状态。

正如你刚才看到的，你可以指定泛型函数的类型参数是你允许的范围。

### 8. 默认类型

每次调用`makeState()`时都要指定`<string>`或`<number>`这样的类型，这很烦人。

所以我有个想法。我们能不能让`<number>`成为`makeState()`的默认类型参数？我们想让它变成这样，如果`S`没有被指定，它将被默认设置为`number`。

为了实现这一点，我们可以通过在最后添加`=number`来指定`S`的默认类型。这有点像为普通函数参数设置默认值，不是吗？

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8f356b4a7fbe449d8b0d51f1809d617c~tplv-k3u1fbpfcp-watermark.image?)

通过这样做，你可以在不指定类型的情况下创建一个纯数字的状态。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/02dee5473500450d8c6cd06f5acb2b4f~tplv-k3u1fbpfcp-watermark.image?)、

### 9. 快速回顾：就像普通函数参数一样

我们已经完成了本文约三分之二的内容。在我们继续之前，让我们做一个简单的回顾。

你应该记住的是，**泛型就像普通的函数参数**。不同的是，普通函数参数处理的是**值**，而泛型处理的是**类型参数**。

例1: 例如，这里有一个普通的函数，可以传入任何类型的值。

```ts
// Declare a regular function
function regularFunc(x: any) {
    // You can use x here
}
// Call it: x will be 1
regularFunc(1)
```

类似地，你可以声明一个带有类型参数的泛型函数。

```ts
// Declare a generic function
function genericFunc<T>() {
// You can use T here
}
// Call it: T will be number
genericFunc<number>()
```

例2：在常规函数中，你可以这样指定一个参数的类型。

```ts
// Specify x to be number
function regularFunc(x: number)
// Success
regularFunc(1)
// Error
regularFunc('foo')
```

同样地，你可以指定泛型函数的类型参数允许的内容。

```ts
// Limits the type of T
function genericFunc<T extends number>()
// Success
genericFunc<number>()
// Error
genericFunc<string>()

```

例3：在常规函数中，你可以这样指定一个参数的默认值。

```ts
// Set the default value of x
function regularFunc(x = 2)
// x will be 2 inside the function
regularFunc()
```

同样地，你可以为一个泛型函数指定默认类型。

```ts
// Set the default type of T
function genericFunc<T = number>()
// T will be number inside the function
genericFunc()
```

泛型并不可怕。**它们就像普通的函数参数，但它处理的是类型，而不是值**。如果你理解了这么多，你就可以继续看下去了！
