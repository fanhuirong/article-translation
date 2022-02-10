> TypeScript Tutorial for JS Programmers Who Know How to Build a Todo App
>
> 原文链接： <https://ts.chibicode.com/todo>

> 作者的话：
> 我写教程是为了帮助初级程序员学习TypeScript。我的教程可能对有经验的程序员不那么有用。
>
> 为什么要针对初级程序员？随着 TypeScript 的普及，越来越多的初级程序员将学习它。然而，我注意到许多现有的教程对初级程序员并不那么友好，因此，我尝试提供一个更友好的学习方案。

## 通过构建Todo应用来学习TypeScript

在2011年，Backbone.js是最流行的JavaScript库之一（React在2013年问世；Vue在2014年）。
当人们在学习Backbone.js时，许多人（包括我自己）是通过构建一个todo应用来学习的。官方文档包括一个用Backbone.js构建的todo应用实例，许多人通过阅读其精美的注释源代码来学习。

在当时，通过建立一个todo应用程序来学习一个JavaScript库是一个新的想法，并由Backbone.js（以及随后的其他库）推广开来。它启发了TodoMVC，它展示了使用不同JS库构建的todo应用。今天，许多教程，如Redux的官方教程，都涉及到构建一个todo应用程序。

但TypeScript教程呢？谈到TypeScript，涉及构建todo应用的教程并不多，我认为这错过了很多机会。构建一个todo应用是学习前端的一个好方法，许多JS程序员已经知道如何构建一个todo应用，因此应该有更多以todo应用为特色的TypeScript教程。

在本教程中，我将通过下图所示的一个示例todo应用来教授TypeScript的一些有趣部分。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/365356911fe94f99a92d3e75b9b84233~tplv-k3u1fbpfcp-watermark.image?)

在我们开始之前，这里有一些细节：

* 本教程不依赖于任何特定的前端库，所以你是否知道React、Vue或其他一些库并不重要。只要你有基本的JS知识，你就能跟上。没有必要事先了解TypeScript。

* 为了节省时间，我不打算谈论如何设置TypeScript项目--你应该阅读其他教程。对于React，请查看[React+TypeScript Cheatsheets](https://github.com/typescript-cheatsheets/react)。

* 同时为了节省时间，我只介绍TypeScript中最基本的概念。我的目标不是要面面俱到，而是要让你想要学习更多。

这篇文章总共有3个部分。以下是每一节所涵盖的主题。

第1节：类型、只读属性和映射类型\
第2节：数组类型、字面类型和交叉类型\
第3节：联合类型和可选属性\
让我们开始吧!

## Section 1 类型、只读属性和映射类型

### 1.1 将数据转化为用户界面

让我们先来看看数据。像React或Vue这样的UI库本质上所做的是将数据转化为UI。例如，在React中，你把数据指定为道具或状态，它就会根据这些数据渲染出UI。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fb4275e2e81146878131de29522669b1~tplv-k3u1fbpfcp-watermark.image?)

现在，让我们来看看下面这个todo应用程序。你能猜到与这个用户界面相关的数据是什么吗？

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/60634e0b69f34dbca686c72cfb8f5a2e~tplv-k3u1fbpfcp-watermark.image?)

**答**：它是一个对象数组，每个对象都有id、text和done。

```js
// Associated data. If we're using React, this
// would be the component’s props or state
[
    { id: 1, text: 'First todo', done: false },
    { id: 2, text: 'Second todo', done: false }
]
```

下面是每个todo对象里面的内容。

* `id`是每个todo项目的ID。这通常是由后端数据库生成的。
* `text`包含每个todo项目的文本。
* 最重要的是，完成的项目为真，否则为假。

让我们把这个应用程序和它的相关数据一起显示出来。试着勾选和不勾选每个复选框，并看看做了什么变化。**（注：请进入原文进行操作）**

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/be1bb55deb2c435dadfd377ee27a67c8~tplv-k3u1fbpfcp-watermark.image?)

正如你所看到的，当你检查/取消检查一个复选框时，它会更新底层数据（`done`的属性），反过来，用户界面也会被更新。这就是React和Vue等UI库的工作方式。

接下来，让我们看看数据如何被更新。

### 1.2 让我们来实现toggleTodo()

为了实现`check`/`uncheck`的功能，我们需要编写代码来切换单个todo项目的已完成属性。

让我们把这个函数命名为`toggleTodo()`。下面是它应该如何工作。

* 当你在一个单一的todo对象上调用`toggleTodo()`时
* 它需要返回一个新的todo对象，该对象的`done`属性为相反的布尔值。

```js
// Takes a single todo object and returns
// a new todo object containing the opposite
// boolean value for the "done" proprty.
function toggleTodo(todo) {
// ...
}

// Example usage:
toggleTodo({ id: …, text: '…', done: true })
// -> returns { id: …, text: '…', done: false }
toggleTodo({ id: …, text: '…', done: false })
// -> returns { id: …, text: '…', done: true }
```

现在，让我介绍我们的初级开发人员--小黄鸭。他将为我们实现`toggleTodo()`。

```js
// 小黄鸭的实现
function toggleTodo(todo) {
    return {
        text: todo.text,
        done: !todo.done
    }
}
```

让我们检查一下小鸭子的实现是否正确。看一下下面的测试案例。你认为输出会是什么？先试着猜一下，然后按下下面的运行按钮。**（注：请进入原文进行操作）**

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/249cfd6c25a1458fbbd539a606e1f8dc~tplv-k3u1fbpfcp-watermark.image?)

`done`属性正确的变成了假的，但它缺少`id`属性。所以小黄鸭的实现是不正确的。

正确的实现:

```ts
function toggleTodo(todo) {
    return {
        // This line was missing
        id: todo.id,
        text: todo.text,
        done: !todo.done
    }
}
```

现在，问题来了。我们怎样才能防止小黄鸭犯这样的错误？

这就是TypeScript的作用。

### 1.3 类型检查

使用TypeScript，我们可以通过做类型检查来防止犯小黄鸭刚刚的错误。

首先，为我们使用的数据创建一个类型。在我们的例子中，我们需要为一个todo项目创建一个类型。我们将这个类型称为Todo，并使用以下TypeScript语法来定义它。

```ts
type Todo = {
    id: number
    text: string
    done: boolean
}
```

我们可以使用这个类型来检查一个变量是否确实是一个todo项目。做这种检查的TypeScript语法是：`变量名: Todo`。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bbd04f778c744f10bdc06eccada1f7f8~tplv-k3u1fbpfcp-watermark.image?)

它成功地编译了，因为foo的类型与Todo的类型相符。

现在，下面这个例子怎么样呢？

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fd2e80bd140d42848d2f9b7dd30fd49f~tplv-k3u1fbpfcp-watermark.image?)

答案是编译失败，因为缺乏`id`属性。

TypeScript能够让你根据指定的类型对变量进行类型检查，这有助于你尽早发现错误。

### 1.4 在toggleTodo()中使用TypeScript

现在，让我们用TypeScript来防止小黄鸭犯的错误。回顾一下，这里是我们之前创建的Todo类型（id是必须的）。

```ts
type Todo = {
    id: number
    text: string
    done: boolean
}
```

首先，我们指定`toggleTodo()`的输入必须是Todo。我们通过在参数todo旁边添加`:Todo`来做到这一点。

```ts
// Parameter "todo" must match the Todo type
function toggleTodo(todo: Todo) {
// ...
}
```

接下来，我们指定`toggleTodo()`的返回类型也必须是Todo。我们通过在参数列表后添加`:Todo`来做到这一点。

```ts
// The return value must match the Todo type
function toggleTodo(todo: Todo): Todo {
// ...
}

```

现在，让我们复制并粘贴小黄鸭写的代码--没有id属性的代码--看看会发生什么。**（注：请进入原文进行操作）**

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d56c1e8002954182b33573a977cb5c46~tplv-k3u1fbpfcp-watermark.image?)

它编译失败了，因为返回的对象缺少id属性，因此不符合Todo的类型。因此，TypeScript可以防止小黄鸭犯的错误!

为了确保这一点，让我们用正确的代码再试一次。我已经在返回的对象中添加了id属性。**（注：请进入原文进行操作）**

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1c699e6449364d18b58c729005d59b54~tplv-k3u1fbpfcp-watermark.image?)

它编译成功了! 正如你所看到的，TypeScript在防止错误和让你知道一切都有正确的类型方面非常出色。

### 1.5 糟糕的重构

现在，代码已经正常运行了，小黄鸭决定重构`toggleTodo()`。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3f28bbe45460441bb2990bb1dd208512~tplv-k3u1fbpfcp-watermark.image?)

编译成功了，但它实际上是一个糟糕的重构。为什么呢？因为它改变了原来的todo对象。**（注：请进入原文进行操作）**

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/af58237f15d747598a2b4964d47d8af1~tplv-k3u1fbpfcp-watermark.image?)

在对其运行`toggleTodo()`后，`argument`发生了变化。这不是好事，因为我们之前已经说过，`toggleTodo()`必须返回一个**新的todo对象**。它不应该修改参数（输入）的todo对象。

这就是为什么尽管它的编译正确，但小黄鸭的重构仍然是一个糟糕的重构。

### 1.6 只读属性

为了防止一个函数修改其输入，你可以在TypeScript中使用`readonly`关键字。在这里，`readonly`关键字被添加到Todo的所有属性中。

```ts
type Todo = {
    readonly id: number
    readonly text: string
    readonly done: boolean
}
```

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b25fcbf07158453483d101a33b69aba2~tplv-k3u1fbpfcp-watermark.image?)

编译失败! 这是因为`done`被定义为一个只读属性，而TypeScript阻止你更新只读属性。

我们再次看到，TypeScript可以防止小黄鸭所犯的错误。

顺便说一下，我们先前使用的实现将继续工作，因为它没有修改输入的todo项目。

```ts
type Todo = {
    readonly id: number
    readonly text: string
    readonly done: boolean
}

// Earlier implementation: it will continue to
// work because the input todo is not modified
function toggleTodo(todo: Todo): Todo {
    return {
        id: todo.id,
        text: todo.text,
        done: !todo.done
    }
}
```

### 6. `ReadOnly<...>`映射类型

在TypeScript中，还有一种方法可以使一个对象类型的所有属性成为只读类型。

```ts
type Todo = {
    readonly id: number
    readonly text: string
    readonly done: boolean
}
```

上面的代码等价于：

```ts
// Readonly<...> makes each property readonly
type Todo = Readonly<{
    id: number
    text: string
    done: boolean
}>
```

在TypeScript中，如果你在一个对象类型上使用`Readonly<...>`关键字，它会使它的所有属性都是只读的。这通常比手动添加`readonly`到每个属性更容易。

下面是另一个例子。

```ts
type Foo = {
    bar: number
}
type ReadonlyFoo = Readonly<Foo>
// ReadonlyFoo is { readonly bar: number }
```

在TypeScript中，你可以使用`Readonly<...>`这样的关键字来将一个类型转换为另一个类型。在这种情况下，`Readonly<...>`接收一个对象类型（如Todo）并创建一个具有只读属性的新对象类型。

而像`Readonly<...>`这样的关键词被称为**映射类型**。映射类型有点像函数，除了输入/输出是TypeScript类型。

有许多内置的映射类型（如`Required<...>`，`Partial<...>`等）。你也可以创建你自己的映射类型。我不会在本文详细介绍这些主题，你可以自己搜索。

### 1.7 section1 小结： 类型(Types)就像轻量级的自动单元测试

到目前为止，我们已经学到了以下内容:

1. 我们可以定义一个类型以确保一个函数的输入和输出是正确的类型。
2. 我们可以使用`readonly`关键字来确保一个对象的属性不被修改。

在JavaScript中，你需要写单元测试来测试这些东西。但TypeScript可以自动检查它们。所以在某种意义上，TypeScript的类型就像轻量级的单元测试，在你每次保存（编译）代码的时候都会运行。(当然，这个比喻是简化的。你还是应该用TypeScript写测试！)

当你使用一个UI库并需要转换数据时，这一点特别有用。例如，如果你使用React，你将需要在状态更新中转换数据。当你把数据从一个父组件传递给它的子组件时，你也可能需要转换数据。TypeScript可以减少这些情况下产生的bug。

最后，我们了解到，我们可以使用像`Readonly`这样的映射类型，将一种类型转换为另一种类型。

## Section 2 数组类型，字面量类型以及交叉类型（Array Types, Literal Types, and Intersection Types）

### 2.1 将所有项目标记为完成状态

让我们来谈谈我们的todo应用程序的一个新功能。"将所有项目标记为完成状态"。试着按下下面的 **`Mark all as completed`**。**（注：请进入原文进行操作）**

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6611ee6877a241ceaf4ae78e4c48ce71~tplv-k3u1fbpfcp-watermark.image?)

在按下 **`Mark all as completed`** 后，所有项目的属性 `done` 最后都变成了 `true`。

让我们用TypeScript来实现这个功能。我们将编写一个名为`completeAll()`的函数，它接收一个`todo`项目数组，并返回一个新的`todos`数组，其中属性`done`都是`true`。

```ts
// Takes an array of todo items and returns
// a new array where "done" is all true
function completeAll(todos) {
// ...
}
```

在实现它之前，让我们指定这个函数的输入/输出类型，以防止出现错误。

### 2.2 为`completeAll()`添加类型

对于`completeAll()`，我们将使用`Todo`类型的`Readonly`版本。

```ts
type Todo = Readonly<{
    id: number
    text: string
    done: boolean
}>
```

首先，我们要指定 `completeAll()` 的参数类型，即 `Todo` 项目的数组。为了指定一个数组类型，我们在类型旁边加上`[]`，如下所示。

```ts
// Input is an array of Todo items: Todo[]
function completeAll(todos: Todo[]) {
// ...
}
```

第二，让我们指定返回类型。它也将是一个`Todo`项目的数组，所以我们将使用和上面一样的语法。

```ts
// Output is an array of Todo items: Todo[]
function completeAll(todos: Todo[]): Todo[] {
// ...
}
```

第三，我们要确保`completeAll()`返回一个新的数组，而不是修改原始数组。

因为我们之前用`Readonly<...>`定义了`Todo`，所以数组中的每个`todo`项目都已经是只读的。然而，**数组本身还不是只读的**。

**为了使数组本身成为只读的，我们需要给`Todo[]`添加`readonly`关键字**，像下面这样。

```ts
// Make input todos as readonly array
function completeAll(
    todos: readonly Todo[]
): Todo[] {
// ...
}
```

所以对于数组，我们使用`readonly`关键字，而不是`Readonly<...>`映射的类型？

是的，我们对数组使用`readonly`关键字。这样做，TypeScript将防止你意外地修改数组。

```ts
// After declaring todos as readonly Todo[],
// the following code WILL NOT compile:
// Compile error - modifies the array
todos[0] = { id: 1, text: '…', done: true }
// Compile error - push() modifies the array
todos.push({ id: 1, text: '…', done: true })
```

事实上。在实现`completeAll()`之前，我们还想做一件事。让我们来看看那是什么!

### 2.3 CompletedTodo类型

看一下下面的代码。除了`Todo`类型之外，我们还定义了一个新的类型，叫做`CompletedTodo`。

```ts
type Todo = Readonly<{
    id: number
    text: string
    done: boolean
}>

type CompletedTodo = Readonly<{
    id: number
    text: string
    done: true
}>
```

新的`CompletedTodo`几乎与`Todo`相同，只是它拥有`done: true`而不是`done: boolean`。

在TypeScript中，你可以在指定一个类型时使用精确的值（如true或false）。这被称为**字面量类型**。

让我们来看看一个例子。在下面的代码中，我们把`CompletedTodo`添加到一个`done：false`的`todo`项目上。让我们看看当你按下编译它时发生了什么。**（注：请进入原文进行操作）**

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/499591f401ad4b4ca28a2f54bb362bb0~tplv-k3u1fbpfcp-watermark.image?)

它编译失败了，因为`done`的值不是`true`。通过使用字面量类型，你可以准确地指定一个属性允许的值。

回到 `completeAll()`，我们可以指定 `completeAll()` 的返回类型是一个 `CompletedTodo` 的数组。

```ts
// Returns an array where "done" is all true
function completeAll(
    todos: readonly Todo[]
): CompletedTodo[] {
// ...
}
```

通过这样做，TypeScript将迫使你返回一个`done`属性都是`true`的`todo`项目数组--如果不是，将导致编译错误。

### 2.4 交叉类型

问题：`Todo`和`CompletedTodo`之间似乎有一些重复的代码。我们能重构他们吗？

好问题！如果你仔细观察，会发现`Todo`和`CompletedTodo`有相同的`id`和`text`类型。

我们可以通过使用TypeScript的一个功能，即**交叉类型**，来重复代码。

在TypeScript中，你可以使用 **`&`** 符号来创建两种类型的交集类型。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3c665946a8224ce6b161551a06ba2063~tplv-k3u1fbpfcp-watermark.image?)

交叉类型`A&B`是一个具有A和B的所有属性的类型，下面是一个例子。

```ts
type A = { a: number }
type B = { b: string }
// This intersection type…
type AandB = A & B
// …is equivalent to:
type AandB = {
  a: number
  b: string
}
```

此外，如果第二种类型比第一种类型更具体，那么第二种类型将**覆盖**第一种。这里有一个例子。

```ts
// They booth have a property foo,
// but B’s foo (true) is
// more specific than A’s foo (boolean)
type A = { foo: boolean }
type B = { foo: true }
// This intersection type…
type AandB = A & B
// …is equivalent to:
type AandB = { foo: true }
```

我们可以应用这个想法来更新`CompletedTodo`的定义。我们将用`Todo`来定义`CompletedTodo`，像这样:

```ts
type Todo = Readonly<{
    id: number
    text: string
    done: boolean
}>

// Override the done property of Todo
type CompletedTodo = Todo & {
    readonly done: true
}
```

通过上述操作，你可以定义`CompletedTodo`，使其具有与`Todo`除了`done`以外相同的属性，而不需要重复代码。

摘要：就像JavaScript有布尔运算符，如 **`&&`**，TypeScript有类型运算符，如 **`&`**，让你结合两种类型。

### 2.5 最终实现 completeAll()

我们终于准备好实现 `completeAll()`了。下面是代码--试着按下 **【Compile】**按钮 !**（注：请进入原文进行操作）**

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b4f2a350cd974e5d97bb3a6462fc378f~tplv-k3u1fbpfcp-watermark.image?)

它已经成功编译了! 让我们在一个示例的todo列表上运行这个函数。按下 **【Run】**按钮 **（注：请进入原文进行操作）**

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e2338265aaf84876ac064e5305749a7c~tplv-k3u1fbpfcp-watermark.image?)
正如预期的那样，`done`都变成了 `true`。

现在，让我们看看如果我们犯了一个错误，把`done`设置为`false`会怎么样。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/23af9b69528e42c7bd931597088a724c~tplv-k3u1fbpfcp-watermark.image?)

它编译失败了，因为 **`CompletedTodo`** 必须拥有 `done：true`属性。再一次，TypeScript提前发现了一个错误。

这一节就到此为止吧 通过使用`completeAll()`和React这样的UI库，我们可以构建我们之前看到的“**`Mark all as completed`**”的功能。

### 2.6 section2 小结：TypeScript可以处理数组和精确值

在本节中，我们已经了解到TypeScript可以处理数组和精确值。

1. 我们可以通过添加`[]`来指定一个数组类型。我们也可以将一个数组设置为只读的。
2. 我们可以使用**字面量类型**来准确地指定一个属性允许使用的值。
3. 最后，我们了解到，我们可以使用**交叉类型**来覆盖一些属性，用来消除重复的代码。

在下一节（也是最后一节），我们将看一下TypeScript最强大的功能之一，**联合类型**`Unions`。
